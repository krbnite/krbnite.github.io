---
title: Parallel Processing in Python (the Multiprocessing Module)
layout: post
---

Here's the gist: by default, the Python interpreter is single-threaded (i.e., a serial processor). This is
technically a safety feature known as the Global Interpreter Lock (GIL): by maintaining
a single thread, Python avoids conflict.  Each computation waits in line to take its turn.  Nobody cuts
the line. There is no name calling, spitting, or all-out brawls when things are taking too long. Everyone's friendly, 
but no one is happy!

Well, dear Reader -- time to take out the multithreading module and make your computations smile.  

## Multithreading vs Multiprocessing (in General)
There are two basic approaches to parallel processing: use multiple threads or use multiple 
processes.  They have their own strengths and weaknesses.

From what I've read, my understanding is that multithreading is a lightweight approach, which allows
for responsive applications, and is great for I/O-bound applications.  However, of the two, multithreading seems 
more difficult to get right. For example, conflicts can occur since memory is shared
between threads; to avoid this, one must be diligent and meticulous with their code (e.g., conform to the
Queue model...).   

Multiprocessing has more overhead, but is safe from conflict since each process runs independently.  This
technique takes advantage of multiple cores and CPUs on your computer, so is great for CPU-bound
applications.  From what I can tell, if you're new to both approaches and have to pick one, multiprocessing wins 
out by far -- setting up multiple processes to run independently of each other is far simpler than worrying
about 
[whether to use a monitor or a semaphore for thread synchronization](http://www.programmerinterview.com/index.php/operating-systems/monitors-vs-semaphores/).

To think of it cartoonishly, threads are all related to each other: they are the children of a single 
process.  When one thread does something stupid (e.g., crash!), it brings down shame on the whole family
(all the threads can crash!).  However, processes don't even really know each other: if a process does something
stupid (e.g., crash!), the others might not even hear about it, never mind feel any shame (they'll just keep running,
doing their own thing).

To think of it less cartoonishly, let's discuss threads and processes.  Here is a quick summary I put together
based on [this article](http://www.programmerinterview.com/index.php/operating-systems/thread-vs-process/):
> * A process is an executing instance of an application
> * A thread is a component of a process
>   - or as the article puts it: a thread is a path of execution within a process
>   - or on [Wikipedia](https://en.wikipedia.org/wiki/Thread_(computing)): a thread of execution is the smallest sequence of programmed instructions that can be managed independently by a scheduler
> * A process can contain multiple threads
>   - When a process is started, the primary thread of that process begins
> * A thread can do anything a process can do, so in a sense can be considered a "lightweight" process
> * threads within the same process share the same resources / address space, whereas different processes do not
>   - multi-threading pro: threads can read from and write to the same data structures and variables
>   - multi-threading con: threads can read from and write to the same data structures and variables (conflicts!)
>   - multi-processing pro: processes do not generally read from and write to the same data structures and variables (no conflicts!)
>   - multi-processing con: processes do not generally read from and write to the same data structures and variables (inter-process communication can be difficult and resource-intensive)


Communication between processes – also known as IPC, or inter-process communication – is quite difficult and resource-intensive.

## Multithreading vs Multiprocessing (in Python)
At the risk of sounding like I just learned all this today, the Global Interpreter Lock (GIL) in Python actually 
reduces any advantages of threading even more.  



```python
from multiprocessing import Pool
p = Pool(5)
def square(x): return x*x
p.map(square, [2,3,4])
p.map(square, [10,21,43])
# After you're done playing
p.terminate()
```

I probably should expand on this, eh?
