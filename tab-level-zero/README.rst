=========================================================
Level Zero Technical Advisory Board Meeting Meeting Notes
=========================================================

2022-6-9
========

Agenda
------

.. list-table::

  * - Topic
    - Title
    - Time
  * - `Intro about Level Zero TAB & Roadmap`_
    - Paul Petersen, Intel
    - 20 min
  * - `Level Zero Specification & How to Participate`_
    - Zack Waters, Intel
    - 20 min
  * - `Discussion Topic:  Separation of Sysman from core Level Zero APIs`_
    - Ben Ashbaugh, Intel
    - 20 min
  * - Wrap up, Question & Answer
    - All
    - 10 min


Attendees
---------

.. list-table::

   * - Robert Cohn, Intel
     - Alastair Murray, Codeplay
     - Jaime Arteaga Molina
   * - Ben Ashbaugh, Intel
     - Benie, Codeplay
     - Brice Goglin
   * - Arlin Davis, Intel
     - En Shao (ICT)
     - Ravindra Babu Ganapathi, Intel
   * - Alexia Ingerson, Intel
     - Juan Fumero, University of Manchester
     - Rahul Khanna, Intel
   * - Cheol Kim, Intel
     - Sergey Maslov, Intel
     - Michal Mrozek, Intel
   * - Servesh Muralidharan, Argonne
     - Paul Petersen, Intel
     - Raja Apphuswamy, EURECOM
   * - Laura Reznikov, Intel
     - Romain Dolbeau
     - Xinmin Tian, Intel
   * - Tim Besard, Julia Computing
     - Peng Tu, Intel
     - Brice Videau, Argonne
   * - Vincent Casillas
     - Michael Voss, Intel
     - Zack Waters, Intel
   * - Jianxin Xiong, Intel
     - Brandon Yates, Intel
     - Russel Mcquire, Intel
   * - Kevin Harms
     -
     -


Intro about Level Zero TAB & Roadmap
------------------------------------

`Slides <presentations/l0-tab-intro.pptx>`__

.. notes for the topic

Q: Why did you not use Buffers?

A: While it is technically possible, different Python classes would
need to be created for every supported buffer data type as the buffer
and accessor type definitions require the type of the underlying
elements. We can get around the issue by using “untyped” buffers, but
that brings its own challenges as partitioning of buffers can lead to
loss of precision and incorrect results.


Level Zero Specification & How to Participate
---------------------------------------------

`Slides <presentations/22ww24_LevelZeroSpec_TAB.pptx>`__

* Call to action

  * Contribute to specification

    * github issues
    * contribute changes

  * help us evolve level zero

    * what are the new features

  * topics for future TAB meetings

Q: What is relationship between spec and implementation?

   Spec without implementation led to errors that are hard to fix. Now
   we wait for implementation before finalization. Prove out a feature
   as an extension, even if inclusion in spec is the goal.

Discussion Topic:  Separation of Sysman from core Level Zero APIs
-----------------------------------------------------------------

* sysman (system management) APIs are tightly coupled to core
* Problem

  * need way to enable sysman without an environment variable
  * application need sysman properties, don't want to replicate sysman
    in core api
  * some application don't need sysman without core APIs

* proposals

  * separate initialization APIs
  * separate drivers
  * make sysman support optional in API

* discussion topics

  * does the proposal solve the problem?
  * is backwards compatible required?
  * do we need to separate privileged/unprivileged sysman
  * do other tools need to be decoupled?

Q: How much separation would this mean for the implementation?

Q: Will separation help me to build a smaller level zero?

   Yes, sycl does not use a lot of the features in sysman

Q: Decoupling is a good idea. Can use uuid to know if 2 devices are
   the same. This is the only solution--being able to disable sysman
   whenever we want depending on the tools.

Q: Do we need to continue supporting the environment variable?

   API could override ZES_INIT=0

Q: Could decoupling lead to different versions being used?

Q: What is the overhead of zes_init?

   Don't put them in a tight loop

Q: If you decouple debugging, separate zed_init, or use mask?

   Could go either way.

Q: Today it is accelerator focused, could be used for other
   devices. Have you looked of decomposing sets of API so that optional
   fucntionality allows it.

   We have been considering that for 2.0. Want to hear feedback about
   this topic.

Q: What is the timeframe for 2.0?

   Not this year. Maybe in the next couple years. Which means we need
   to start thinking about it now.

Q: Topic on visibility of devices/affinity mask that makes devices
   available: https://github.com/oneapi-src/level-zero-spec/issues/1

   We will be presenting a proposal. We will make it a topic for a
   future meeting.

Q: To support heterogeneity, we need something more flexible than the
   current affinity mask. Don't see clear device abstraction.

Q: Any plans to allow for vectorized representation of SPIR-V for
   front ends that already have efficient vectorized code generation?

   I think this is already supported? If it isn't, is this an
   implementation question, or a spec question? Let's focus on spec
   questions here.

Q: What should drive level 2.0? Need features to build runtimes on top
   of level zero. Runtimes use l0 inefficiently, polling, many
   threads, etc. Need async programming models. L0 should be
   sufficient or provide capability to do that on top. Seen that issue
   with hip on top of l0. Difficult to get right, error prone,
   especially multi-threading. Everybody needs to implement the same
   thing, and does a poor job. Users should not handle helper threads.

Q: Can we show that opencl API on top of L0 has same efficiency?
   People should not have to figure out tricks to get performance.
