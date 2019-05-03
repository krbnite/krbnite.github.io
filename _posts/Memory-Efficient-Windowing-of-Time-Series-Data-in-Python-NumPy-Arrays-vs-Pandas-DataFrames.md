---
title: Memory-Efficient Windowing of Time Series Data in Python: 1. Memory Strides in NumPy
layout: post
tags: time-series pandas numpy python 
---

Let's say you have time series data, and you need to cut it up into small, overlapping
windows.  In the past, I've done this for spectral analysis (e.g., short-time Fourier transform),
and more recently when working with recurrent neural networks.

In the past, when doing spectral analysis of geomagnetic time series, I was working with
1-second sampled data streams, which amounts to 86,400 samples per day.  For any given experiment
or exploration, I might be working with 4-5 magnetometers with 3 axes each, and looking over
intervals spanning a day to months.  For simplicity, let's assume 64-bit numbers (8 bytes) streaming
from 4 magnetometers, with 3 axes each, sampling at 1 Hz (86400/day), over 30 days:

```
# 30 days of data
8 * 4 * 3 * 86,400 * 30 = 248,832,000 bytes
```

That's 249 MB -- not too bad.  

Ok, but that's not how things really worked since I windowed the data using 1-hour (3600-data point)
windows stepped forward in time every 10 minutes (600 data points).  There was no fancy memory
mapping happening here: I was replicating the shit out of that data with not a care in the world!

So how much data did I really have in memory? Well, an easy way to compute that is to (i) compute
how much memory a 1-hour window likely takes, then (ii) multiply that by the number of windows.  We
already know how to compute 1-hour of data -- just replace `86400 * 30` by `3600` in the equation
above:

```
# 1 hour of data
8 * 4 * 3 * 3600 = 345,600 bytes
```

Perhaps a trickier question is, how many windows will we have?

I think the easy way to think through this is to consider just two days of data.  Consider
the starting time of each window. On the first day, the first window starts at 00:00:00, and
there is a window that begins every 10 minutes thereafter for the entire day.  So we have
00:00:00, 00:10:00, 00:20:00, ..., 23:30:00, 23:40:00, 23:50:00.  That is 6 windows per hour
for 24 hours, or `24 * 6 = 144` windows on the first day.  Now remember, each of these windows
are 1-hour long, so the second days will have a few less data windows.  Specifically, the
last window will be 23:00:00, assuming we are only using complete windows (not edge wrapping,
zero padding, etc).  In other words, the second day of this 2-day interval would have 5 fewer
windows than the first day: 139.  

Now, the trick is to realize that all days in any multi-day span will have as many windows as
the first day, except the last day of span.  So, for a 30-day interval, we would have
`144*29 + 139` windows, or `4315` for those of you whose eyes just crossed.

So how much data did I have in memory?

```
# 30 day interval of windows
345600 (bytes/window) * 4315 (windows) = 1,491,264,000 bytes
```

That is, about 1.5 GB.

Much bigger than the original 249 MB, but still not very big.  I could do it on my laptop with
no tricks!

Ok, but what if the time series had been sampled at 100 Hz?  That would be 100.5 GB.  In that case,
I would probably have to start doing some tricks.  For example, the easiest would be to not
create a massive array, but simply run the window over the regular array, then compute power spectra and
write to a file as you go.

A crazier and cooler trick is that allows you have your cake (massive array) and eat it too (same
memory footprint as original array) is to using memory mapping tricks, as NumPy's `as_strided` function
does.

Let's look at nother example.

I'm currently working with multiple wearable devices, creating 
30 - 100+ 400 Hz data streams simultaneously, for intervals spanning tens of minutes.

```python
# 50 400-Hz data streams spaning 20 minutes
rate_per_sec = 400
rate_per_min = 60 * rate_per_sec
num_streams = 50
num_minutes = 20
bytes_per_data_point = 8
total_num_data_points = num_minutes * rate_per_min * num_streams
memory_estimate = total_num_data_points * bytes_per_data_point
print(memory_estimate)
  192000000
```

That is ~192 MB, or ~ 0.2 GB.

Not bad, but what if we create 2-second (800-data point) windows stepped forward in time every 0.5 seconds 
(200 data points)?

We can do a similar analysis of that above.  In the first minute, the first
data window starts at 00:00:00.00, the second at 00:00:00.5, and so on until the last data window in that
minute, beginning at 00:00:59.5.  In other words, the first minute will have 120 data windows.  We know that
last minute in this N-minute interval will have fewer windows, but how many fewer?  The last full 
2-second window begins at xy:zu:58.0.   It has 3 fewer data windows.    So the total number of windows
this time will be `(N-1)*120 + (120-3)`.  If N=20, then we have `19*120 + 117` data windows, or `2,397` for
those of you asleep at the keyboard!

```python
# 2-second data window for 50 data streams (8B/dp)
win_mem = 8 * 50 * 800 # 320,000

# Memory estimate for 20 mins of 2-sec windows stepped @ 0.5 sec
num_windows = 2397
win_mem * num_windows
  767040000
```

That is, 767,040,000 -- or just around 0.8GB.  That makes sense since we are representing most
data points in 4 windows, 4x the original array.  In fact, this is plainly a simpler way
to estimate the memory of one of these massively stacked array: multiply the original memory
by how many `window_size / step_size`.  For the magnetometers, this factor was `3600/600 = 6` and
for `800/200 = 4` in the wearables case.  

Anyway, now consider this same wearable project for 10 people simultaneously: it becomes almost 10 GB
of data.  

Point is the same as above: one needs tricks!

And so, again I plug NumPy's `as_strided` function found in `numpy.lib.stride_tricks`.

Let's look into it a bit.

```
import numpy as np
import pandas as pd

n = 86400 * 30   # 30 days of 1-Hz data

df = pd.DataFrame({
  'mag1_x': np.random.randn(n), 'mag1_y': np.zeros(n), 'mag1_z': np.ones(n),
  'mag2_x': np.random.randn(n), 'mag2_y': np.random.gamma(1,1,n),  'mag2_z': np.random.randn(n),
  'mag3_x': np.random.randn(n), 'mag3_y': np.zeros(n), 'mag3_z': np.ones(n),
  'mag4_x': np.random.randn(n), 'mag4_y': np.random.gamma(1,1,n),  'mag4_z': np.random.randn(n),

})

# Memory Estimate
f'{round(df.memory_usage().sum()/1.0e6,2)} MB'
  248.83 MB
```

"Wow, Kevin -- that is the same that you estimated above!"

Did you dare question the veracity of my truth, Dear Reader?!

But seriously, what a great sanity check.  Now let's brute force window this array and
check the memory.  

..........TO BE CONTINUED............

