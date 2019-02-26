---
layout: post
title: Python in Parallel
tags: python wwe
---

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

-------------------------

## Celery
From [celeryproject.org](http://www.celeryproject.org):
> Celery is an asynchronous task queue/job queue based on distributed message passing.	It is 
> focused on real-time operation, but supports scheduling as well. The execution units, called tasks, are 
> executed concurrently on a single or more worker servers using multiprocessing, Eventlet,	or 
> gevent. Tasks can execute asynchronously (in the background) or synchronously (wait until ready).

To install:
```
# -U, --upgrade: Upgrade all packages to the newest available version
pip install -U Celery
```

Celery recommends using RabbitMQ as its broker (message transport support), but supports other brokers 
as well: Redis, [Amazon SQS](https://aws.amazon.com/sqs/), Beanstalk, MongoDB, and CouchDB.  One can also use a database to fill this role 
through using SQLAlchemy or the Django ORM. 

### RabbitMQ
From a [tutorial](https://www.rabbitmq.com/tutorials/tutorial-one-python.html) on [rabbitmq.com](https://www.rabbitmq.com):
> RabbitMQ is a message broker: it accepts and forwards messages. You can think about it as a post office: when you 
> put the mail that you want posting in a post box, you can be sure that Mr. Postman will eventually deliver the 
> mail to your recipient. In this analogy, RabbitMQ is a post box, a post office and a postman.
>
> The major difference between RabbitMQ and the post office is that it doesn't deal with paper, instead it accepts, 
> stores and forwards binary blobs of data ‒ messages.


### Some Relevant Wikipedia Links
* [Message Queue](https://en.wikipedia.org/wiki/Message_queue)
* [Message Queuing Service](https://en.wikipedia.org/wiki/Message_queuing_service)
* [Message-Oriented Middleware (MOM)](https://en.wikipedia.org/wiki/Message-oriented_middleware)
* [Producer-Consumer Problem](https://en.wikipedia.org/wiki/Producer%E2%80%93consumer_problem)
* [Publish-Subscribe Paradigm](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)


### Btw, the Lynda Course...
The first video on Celery lists and recites three quasi-vague statements about what Celery is,
shows how to download Celery (pip),  briefly displays a diagram about the publish/subscribe paradigm,
and mentions to download RabbitMQ "at this link" and Flower "at this link."  The second video 
shows a weak, unmotivated use case...  

Talk about "intro" level material on a topic.  You can accidentally learn
more about Celery from trying to read the first paragraph of the Wikipedia article off of a co-worker's computer 
screen as you pass by on the way to the bathroom.  

Maybe that's good though...  The Lynda course serves as a course outline in this regard, and it's up to 
you to fill in the details.  My recommendation would be to cover 2-3 videos or so
a morning, and spend most of your time googling terms and reading relevant tutorials.  

The course instructor says at the end of the Celery's first video, "Awesome! We now know how to use Celery to distribute
tasks." He definitely should have waited to at least the end of the second video to say that!  
Something positive-sounding like this is uttered at the end of each information-rarefied video, which makes the course
feel like it lacks self awareness: not sure what lesson on Celery he thought he gave, but I'd never leave
the first video thinking, "Awesome! I now understand what Celery is and why I would use it in addition to, or instead of,
Python's multiprocessing module and all the other shit we've blasted through so far." And not even after the second video.

Again, I feel bad making fun of the course because I also appreciate that it exists and was there for me to use 
as a guide.  But to call it a course is a little far-fetched.







#### Python/RabbitMQ Tutorials
* [Getting Started]
* [Work Queues](https://www.rabbitmq.com/tutorials/tutorial-two-python.html)
* [Publish/Subscribe](https://www.rabbitmq.com/tutorials/tutorial-three-python.html)
* [Routing](https://www.rabbitmq.com/tutorials/tutorial-four-python.html)
* [Topics](https://www.rabbitmq.com/tutorials/tutorial-five-python.html)
* [Remote Procedure Call (RPC)](https://www.rabbitmq.com/tutorials/tutorial-six-python.html)
