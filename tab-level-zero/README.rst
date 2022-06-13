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
     - Jaime Arteaga Molina, Intel
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

- There is contribution guideline on the Github.
- What is the right cadence, frequency?  What kind of interest level?
  Any feedback?  oneAPI is trying to solve a difficult problem.
- LevelZero spec has been shared from beta 1.0
- Does oneAPI need a Unified Runtime?  We might want to partition?  We
  could simplify it as a hardware device layer.  Device Abstraction
  should be required.
- What is the primitive function that we have not been covering so far
  to support your fav language?
- Do we need special support for the CPU?  CPU device driver or
  resource management?  Leverage common components…memory structure
  for heterogeneous compute platforms.  Proposing Unified Runtime API.
  It's a fairly radical sw infra…relatively major change to our sw
  structure.  Revolutionary change should be required.  Need a smooth
  transition to discuss and agree the directions at TAB and other
  places.


Level Zero Specification & How to Participate
---------------------------------------------

`Slides <presentations/22ww24_LevelZeroSpec_TAB.pptx>`__

- Spec.1.4 has been published in May
- Starting with Level Zero spec. v1.5.
- Scripts generate spec and headers from API spec and programming
  guide; covering YAML, headers, implementation to make sure codes are
  compiled, and API spec.  Programming guides are validated by script.
- Trying to ensure backward compatibility: minor version increment,
  major version increment
- Next steps: release spec dev framework, post spec issues from
  internal repo, organize candidate spec update for spec v1.5.
- Pls contribute to Level Zero spec, help us evolve Level Zero, and
  propose new feature required. What Level Zero topics should be
  covered in future TAB meeting?
- What's the relationship between Implementation and spec?  Spec
  without implementation led to errors that are hard to fix. Now we
  wait for implementation before finalization. Prove out a feature as
  an extension, even if inclusion in spec is the goal.

Discussion Topic:  Separation of Sysman from core Level Zero APIs
-----------------------------------------------------------------

`Slides <presentations/22ww24_Sysman_TAB.pptx>`__

- Currently LZ Sysman is tightly coupled to LZ Core APIs, a problem is
  that it's unclear who should enable Sysman, and how and when.
- Proposal from Ben: Add a zeslnit() for initializing sysman,
  independent of zelnit(), add query functions for sysman driver and
  device support, add function to get sysman handle from coreAPI
  handle, consider enumerating sysman separately from core APIs.
- Questions from Ben: right direction?  How important is backward
  compatibility?  Do we need to separate privileged sysman form
  unprivileged sysman?  How to handle ZES-ENABLE_SYSMAN_LOW_POWER?  Do
  we need to decouple other tools as well (e.g. debugging)?
- Servesh - how will we separate Sysman from the Core?  We'd enable
  decouple…but answer is it all depends.
- Peng Tu - will it make easier for a developer to enable Level Zero
  in that way?  Currently most SYCL users don't use Sysman, so we
  still need more feedback.
- Brice Videau (from Argonne) - Agree.  this is only way…decoupling is
  a good idea.
- Juan (from Manchester) - Running different version should be ok?
  Implementation details.  Interchanging devices.
- Why do we need to initialize Sysman?  We want the architecture to be
  able to initialize separately.  You need a specific dependency.  The
  right flow is to check the dependency and initialize Sysman.  It's
  more like requirement.
- Servesh: It seems like the target is for accelerators…future is
  device sitting in CPU and other devices probably.  Have you thought
  about supporting the demand for those?  We'll make sure that ver 2.0
  would be refactoring some functionality to cover such heterogeneous
  platform support requirement.  Specs will be evolved but when?  Not
  this year…but next few years.  Need to be more flexible for
  supporting heterogeneous environment.  What's the philosophy behind
  Level Zero?  Level Zero needs to be visible so developers can
  flexibly define another abstraction layer higher as needed while
  emulating devices.  We'll need to clean up the challenge going
  forward.
- Brice Videau: What's driving Level Zero ver 2.0?  Runtime is doing
  poor job for enabling Level Zero in general.  Need to cover some
  functionality to make it implemented effciently, but has not seen it
  on Level Zero.  Rebuilding OPEN CL---some members don't like the
  idea.  TAB is a goold place to tackle the issue.


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
