For several days, I've spent my morning watching lynda.com's 
"[Python Parallel Programming Solutions](https://www.lynda.com/Python-tutorials/Python-Parallel-Programming-Solutions/604237-2.html)" 
course in an attempt to better understand threading and multiprocessing, and how they are implemented in
Python.  

The first module of the course was most useful to me.

The second module on threading in Python rotely went over the objects and methods available in
Python's `threading` package.  It was not much more content than the intro-level stuff I've seen
on many blogs and StackExchange posts.  Also, the videos did not really discuss real world
applications beyond the toy examples.  The first half of the next module was on Python's
`multiprocessing` module, which followed the same format.

The last half of the third segment of the course went over MPI and the `mpi4py` module.  Though
this seemed to have the same rote format, it was interesting because it was new to me.  The course
didn't really seem to motivate why I would use MPI over multiprocessing, or vice versa.  After
some cursory googling, my current understanding is that MPI is better used when working with a
computer cluster... broadcast, scatter, gather...

There is an interesting bit on computing over cartesian topologies vs toroidal topologies, etc. This
definitely caught my attention :-p

The next several modules seem like they will be useful for the same reason: concurrent.futures,
asyncio, celery...


## Asyncio
The course best serves as a guide: it throws around terms and shows simplest-case code relating to
each term.  So why not leverage the guide -- write down the terms, google them to learn more, and
ultimately write down some code and motivation for it.

* event loop management
  - event loop
  - event source
  - event handler
* coroutines
  - a coroutine is responsible for a single computational step
  - unlike a subroutine, there is no parent program used to coordinate results
  - there is no supervising functions responsible for calling the coroutines in any particular order
  - that is, coroutines do not depend on each other: a coroutine can be suspended and resumed later, no big deal
* futures
* synchronization primitives
* finite state machine (FSA)
  - aka automaton

---------------------------------

* Some more in-depth Udacity courses:
  - Udacity: [Parallel Programming w/ CUDA](https://www.udacity.com/course/intro-to-parallel-programming--cs344)
  - Coursera: [Parallel Programming](https://www.coursera.org/learn/parprog1)
* More in-depth courses from Carnegie Mellon: 
  - 2016: [Parallel Computer Architecture and Programming](https://scs.hosted.panopto.com/Panopto/Pages/Sessions/List.aspx#folderID=%22a5862643-2416-49ef-b46b-13465d1b6df0%22)
  - 2015: [Parallel Computer Architecture and Programming](https://scs.hosted.panopto.com/Panopto/Pages/Sessions/List.aspx#folderID=%22f62c2297-de88-4e63-aff2-06641fa25e98%22)
* Book Review: http://sbrisard.github.io/posts/20140813-Review_of_Parallel_Programming_with_Python.html
  - sounds like he's describing the Lynda course :-p

