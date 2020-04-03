========================
 TAB-Lang Meeting Notes
========================

2020-03-25
==========

Attendees: David Beckingsale, James Brodman, Robert Cohn, Tom Deakin,
Hal Finkel, Mike Kinsner, Alexey Kukanov, Erik Lindahl, Geoff Lowney,
Antonio J. Peña, John Pennycook, Pablo Reble, James Reinders, Ruyman
Reyes, Alison Richards, Roland Schulz, Timmie Smith, Xinmin Tian

* Github: Robert Cohn

  * We will be publishing TAB presentations materials & notes with
    names on `github
    <https://github.com/oneapi-src/oneapi-tab>`__. Please contact
    `Robert.S.Cohn@intel <mailto:Robert.S.Cohn@intel.com>`__ if you
    have concerns. If you are a watcher on the repo, you will get
    email notification for meeting notes. Follow-up discussions can be
    in the form of github issues.
  * Specification is available on `oneapi.com
    <https://spec.oneapi.com/>`__. DPC++ spec contains the list of
    SYCL extensions with links to github docs describing them.
  * oneAPI open source projects are moving to `oneapi-src
    <https://github.com/oneapi-src/>`__ organization on github.
  * Repo for oneAPI Specification `sources
    <https://github.com/oneapi-src/oneapi-spec>`__ is in same
    org. File issues if you have detailed feedback about the
    specifications.
 
* Unified Shared Memory (USM): James Brodman

  `Slides <presentations/2020-03-25-USM-for-TAB.pdf>`__

  * Pointer-based memory management, complimentary to SYCL buffers
  * What is the latency for pointer queries?

    * Have not measured, but it requires calls into driver and is not
      lightweight
    * Can it be accelerated with bit masks?
    * Could it be made fast enough so free() could check?

  * Are there any issues when using multiple GPUs?

    * All pointers must be in same context
    * Not likely to work if devices are not all from same vendor
    * peer-to-peer, GPU's directly accessing each other's memory, is
      being considered for inclusion in Level Zero spec, and might be
      added to DPC++ spec
    * non-restricted shared allocations should work fine

  * What about atomics?

    * We are trying to flesh out general details of atomics first, and
      will define USM characteristics after.

  * OMP also uses the name USM, we need a document that
    compares/contrasts the capability

  * Are operations that prefetch (ensure data is resident on a
    specific device) placed in queues? What does 'done' mean?

    * Investigating

  * Are hints suggestions or hard rules?

    * Device is free to define the behavior. Devices vary in their capability.

  * Can you change the flavor of allocation? (shared, device, ..)

    * No. What is the use case?
    * Example: When we are limited by memory capacity, a library may
      want to change the allocation.

2020-03-04
==========

* Follow-up from last meeting: John Pennycook

  * Prototype implementation published as `PR
    <https://github.com/intel/llvm/pull/1236>`__ on github
  * Addressed feedback on types for reductions: assertion checks if
    are accumulating in type different from initial type

* Minimum version of C++: James Brodman `Slides
  <presentations/2020-03-04-TAB-C++-Minimum-Version.pdf>`__

  * Currently C++11, want to move to C++17
  * Considered C++14 + key features
  * Clang default is 14

2020-01-28
==========

`Slides <presentations/2020-01-28-TAB-DPCPPMeeting2_v7.pdf>`__

* Follow-up from last meeting
* Review of group collectives
* Simplifying language for common patterns
  
