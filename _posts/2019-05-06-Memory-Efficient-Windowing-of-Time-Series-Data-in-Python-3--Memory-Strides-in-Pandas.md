---
title: Memory-Efficient Windowing of Time Series Data in Python&#58; 3. Memory Strides in Pandas
layout: post
tags: time-series pandas numpy python easi
---

In the [first post](https://krbnite.github.io/Memory-Efficient-Windowing-of-Time-Series-Data-in-Python-Memory-Strides-in-NumPy/)
in this series, we covered memory strides for default NumPy arrays -- or, more generally, for
C-like, "row major" arrays.  In the [second post](https://krbnite.github.io/Memory-Efficient-Windowing-of-Time-Series-Data-in-Python-NumPy-Arrays-vs-Pandas-DataFrames/),
we showed that a NumPy array can also be F-like (column
major). More importantly, for those who like to switch back and forth between Pandas and NumPy, we found that
a typical DataFrame is F-like (not always, but often -- and in important cases).  We also found
that if one builds a windowing function based on NumPy's `as_strided` assuming a C-like array,
but instead uses F-like arrays in production, one shall be screwed.  

One doesn't like to be screwed in production!

Originally, we built out the `make_views` function in the
[first installation](https://krbnite.github.io/Memory-Efficient-Windowing-of-Time-Series-Data-in-Python-Memory-Strides-in-NumPy/)
of this series.  This function expects C-Like NumPy arrays, but often I work with F-like Pandas
DataFrames, which are built atop F-Like NumPy arrays.  In this post, I generalize the windowing function (more accurately, the "viewing" function) to
delineate between C-like and F-like arrays and act accordingly. 

For convenience, here is the original incarnation of `make_views`:

```python
def make_views(
  arr,
  win_size,
  step_size,
  writeable = False,
):
  """
  arr: any 2D array whose columns are distinct variables and 
    rows are data records at some timestamp t
  win_size: size of data window (given in data points along record/time axis)
  step_size: size of window step (given in data point along record/time axis)
  writable: if True, elements can be modified in new data structure, which will affect
    original array (defaults to False)
  
  Note that step_size is related to window overlap (overlap = win_size - step_size), in 
  case you think in overlaps.
  """
  
  n_records = arr.shape[0]
  n_columns = arr.shape[1]
  remainder = (n_records - win_size) % step_size 
  num_windows = 1 + int((n_records - win_size - remainder) / step_size)
  new_view_structure = as_strided(
    arr,
    shape = (num_windows, win_size, n_columns),
    strides = (8 * step_size * n_columns, 8 * n_columns, 8),
    writeable = False,
  )
  return new_view_structure
```

# The Set Up
Next, we will begin investigating how to generalize `make_views`, but first -- let's 
define some terms, so we can explore some code!

```python
import numpy as np
import pandas as pd
from numpy.lib.strid_tricks import as_strided

# Data
a = [0,1,2,3,4,5,6,7,8,9]
b = [1,1,1,1,1,1,1,1,1,1]
c = [0,0,0,0,0,0,0,0,0,0]

# C-Like NumPy Array
arr = np.concatenate([
  np.array(a).reshape(10,1),
  np.array(b).reshape(10,1),
  np.array(c).reshape(10,1)],
  axis = 1)

# F-Like Pandas DataFrame
df = pd.DataFrame({'a': a, 'b': b, 'c': c})
```

# First Observation: Stride Structure
Let's remind ourselves what the strides of the C-like NumPy array and F-like Pandas DataFrame look 
like:

```python
arr.strides
  (24,8)
  
df.values.strides
  (8,80)
```

Remember, for the C-like array (`arr`), the stride tuple (24,8) means one must move 24 bytes (3 8-Byte floats) to move
down a row within the same column, but only 8 Bytes to move to the next column in the same row.  Again,
a C-like array is "row major", which means the array array is stored as a 1D sequence of its rows, e.g.,
(row1, row2, row3).

Conversely, for the F-like array (`df.values`), the stride tuple (8,80) means that we must only move 8 Bytes
to get to the next row within the same column, and must move 80 Bytes (10 8-Byte floats) to get to the next
column in the same row.  Whereas row elements are close in memory for C-like arrays, it is the column elements
that are close in memory for F-like arrays.  In memory, an F-like array is stored as a 1D sequence of its
columns, e.g., (col1, col2, col3).

Though the arrays differ in terms of which of its elements are near each other in memory, the structure
of the strides tuple itself remains the same:
* strides[0]:  how many Bytes to get to next row within the same column
* strides[1]:  how many Bytes to get to the next column within the same row

# Second Observation: the Strided Stride Structure
The advantage of playing with such a simple toy data set is that we know exactly what to
expect of our windowing procedure.  

## 2-Record Window Stepped Forward Every Record (50% overlap)
So, for example, we know that if we make a 2-record window
stepped forward (in time, in index, etc) every record, that the first three windows will look
like:

```python
array([[[0, 1, 0],
        [1, 1, 0]],

       [[1, 1, 0],
        [2, 1, 0]],

       [[2, 1, 0],
        [3, 1, 0]])
```

This means, all we have to do is play around a bit with the strides parameter in `as_strided`
until we get what we expect.  We already know how to stride on C-like arrays from the first 
installment in this series, but this kind of play comes in handy for the F-like array.

Ultimately, the following produces the desired array:

```python
# Strides for C-like Array
as_strided(arr, shape=(9,2,3), strides=(24,24,8))[0:3] 
  array([[[0, 1, 0],
          [1, 1, 0]],

         [[1, 1, 0],
          [2, 1, 0]],

         [[2, 1, 0],
          [3, 1, 0]]])

# Strides for F-like Array
as_strided(df, shape=(9,2,3), strides=(8,8,80))[0:3] 
  array([[[0, 1, 0],
          [1, 1, 0]],

         [[1, 1, 0],
          [2, 1, 0]],

         [[2, 1, 0],
          [3, 1, 0]]])
```

So we find that, for C-like and F-like arrays with same values and shape, but different strides ((24,8) and (8,80), 
respectively), we can make a 2-record window stepped forward every record using the same recipe:
* the `as_strided` shape parameter is the same for both: (num_wins, win_size, n_cols)
* the `as_strided` strides parameter has the same pattern for both (strides[0], strides[0], strides[1])
  - for C-like array: (24,8) --> (24,24,8)
  - for F-like array: (8,80) --> (8,8,80)

This tells me that generalizing the `make_views` function is going to be much simpler than I 
anticipated!

Btw, another observation: you don't actually have to put `df.values` into `as_strided` -- it knows
what to do with just `df`.  That wasn't a typo in the above code.  

Let's just look at one more example to see if our observations on the `as_strided` input parameters
still hold.

## 2-Record Window Stepped Forward Every 2 Records (nonoverlapping windows)
Again, we can simply write out what the output is supposed to look like.

```
# Window 1
0,1,0
1,1,0

# Window 2
2,1,0
3,1,0

# Window 3
4,1,0
5,1,0
```

From last time, we know what the `shape` parameter should look like:

```python
num_wins = 5
win_size = 2
n_cols = 3
new_shape = (num_wins, win_size, n_cols)
```

Ultimately, we need to figure out the `strides` parameter.  From the first installment, we know
how to do this for the C-like array:

```
step_size = 2
arr_strides = arr.strides
win_stride = step_size * arr_strides[0] # step_size * n_cols * 8
row_stride = arr.strides[0]             # n_cols * 8
col_stride = arr.strides[1]             # 8
view_strides = (win_stride, row_stride, col_stride)
```

This pattern works for F-like arrays too!

```python
# Strides for C-like Array
c_strides = (step_size * arr.strides[0],) + arr.strides
as_strided(arr, shape = new_shape, strides = c_strides)[0:3] 
  array([[[0, 1, 0],
          [1, 1, 0]],

         [[2, 1, 0],
          [3, 1, 0]],

         [[4, 1, 0],
          [5, 1, 0]]])

# Strides for F-like Array
f_strides = (step_size * df.values.strides[0],) + df.values.strides
as_strided(df, shape = new_shape, strides = f_strides)[0:3] 
  array([[[0, 1, 0],
          [1, 1, 0]],

         [[2, 1, 0],
          [3, 1, 0]],

         [[4, 1, 0],
          [5, 1, 0]]])
```

So, that's it.  We already have the code that computes the max number of full windows, leaving
off the last partially-filled window (which would be accidentally filled with random nonsense 
by `as_strided`).  Now we know how to compute the `shape` parameter for both C-like and F-like
arrays (it's the same tuple for both) and the `strides` parameter (it's the same tuple pattern
for both).  Done.  Wow.


# The Generalized `make_views` Function
This is it!  If the `as_strided` function ever confused you, let it confuse you no
more!


```python
def make_views(
  arr,
  win_size,
  step_size,
  writeable = False,
):
  """
  arr: any 2D array whose columns are distinct variables and 
    rows are data records at some timestamp t
  win_size: size of data window (given in data points along record/time axis)
  step_size: size of window step (given in data point along record/time axis)
  writable: if True, elements can be modified in new data structure, which will affect
    original array (defaults to False)
  
  Note that step_size is related to window overlap (overlap = win_size - step_size), in 
  case you think in overlaps.
  
  This function can work with C-like and F-like arrays, and with DataFrames.  Yay.
  """
  
  # If DataFrame, use only underlying NumPy array
  if type(arr) == type(pd.DataFrame()):
    arr = arr.values
  
  # Compute Shape Parameter for as_strided
  n_records = arr.shape[0]
  n_columns = arr.shape[1]
  remainder = (n_records - win_size) % step_size 
  num_windows = 1 + int((n_records - win_size - remainder) / step_size)
  shape = (num_windows, win_size, n_columns)
  
  # Compute Strides Parameter for as_strided
  next_win = step_size * arr.strides[0]
  next_row, next_col = arr.strides
  strides = (next_win, next_row, next_col)

  new_view_structure = as_strided(
    arr,
    shape = shape,
    strides = strides,
    writeable = writeable,
  )
  return new_view_structure
  
```

