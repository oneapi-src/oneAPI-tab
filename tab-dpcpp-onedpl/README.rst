======================================================================
oneAPI Technical Advisory Board Meeting (DPC++ & oneDPL) Meeting Notes
======================================================================

Upcoming Topics
===============

* Error handling
* Function pointers revisited
* [2nd half 2021] oneDPL C++ standard library support

2021-4-21
=========

* Robert Cohn (Intel)
* Romain Dolbeau (SiPearl)
* David Beckingsale (Lawrence Livermore National Laboratory)
* Christian Trott (Sandia National Laboratory)
* En Shao (Institute of Compute Technology, China Academy of Sciences)
* Christian Trott (Sandia National Laboratory)
* Erik Lindahl (Stockholm University)
* Guangming Tan (Institute of Compute Technology, China Academy of Sciences)
* Simon P Garcia de Gonzalo (Barcelona Supercomputing Center)
* Michael Kinsner (Intel)
* Alexey Kukanov (Intel)
* Nevin Liber (Argonne National Laboratory)
* Geoff Lowney (Intel)
* Greg Lueck (Intel)
* Andrew Lumsdaine (University of Washington, Pacific Northwest National Laboratory)
* Pablo Reble (Intel)
* James Reinders (Intel)
* Alison Richards (Intel)
* Ronan Keryell (Xilinx)
* Timmie Smith (Intel)
* Stefan Yurkevitch (ArrayFire)
* Xinmin Tian (Intel)
* Tom Deakin (University of Bristol)
* Umar Arshad (ArrayFire)
* Ruyman Reyes (Codeplay)
* Pradeep Garigipati (ArrayFire)
* Andrew Richards (Codeplay)
* James Brodman (Intel)


oneDPL range-based & async APIs: Alexey Kukanov
-----------------------------------------------

* `Slides <presentations/2021-04-21-oneDPL-for-TAB.pdf>`__
* oneDPL recap
* Notable changes

  * Namespace oneapi::dpl, ::dpl, dropped oneapi::std because of
    usability
  * Algorithms are blocking by default
  * Execution policy

    * device_policy, fpga_policy
    * Implicit conversion to sycl::queue

* Notable implementation-specific additions,
  not yet part of the spec:

  * <random>
  * range-based API
  * asynch API

* <random>

  * Subset of C++ random
  * Generate several RNs at once into sycl::vec
  * Seed + offset lets you generate the same as one at a time API

  * Feedback

    * for_each should not be part of std:

      * Have it for convenience, types prevent confusion with standard

* Range-based API

  * Ranges are new for C++20
  * Used in algorithms, not yet for execution policy
  * Not fully standard-compliant, not based on concepts, no projections
  * Examples:

    * Fancy iterators allow combine into single kernel, but clumsy
    * Ranges allows 1 kernel, more concise

      * Expressed as pipeline of transformations

  * Using with execution policies

    * Range over:

      * Sequence of indexes
      * USM data
      * Buffer

        * With variants for all_read, all_write

    * Looking for feedback on how to make it device copyable


  * oneDPL v2021.3 has 34 algorithms with range-based API

  *  Feedback: happy to see modern C++

* Async api

  * Blocking is default
  * Deferred waiting mode enabled by macro

    * Only for no return value functions
    * Non-standard, will not be part of spec

  * Experimental async

    * Never wait, return future-like object
    * Supports multi-device

  * API

    * Add _async suffix, alternatives: namespace, policy class
    * Taken an arbitrary number of dependencies as arguments
    * Returns an unspecified future-like type

      * Not specific because it is an extension and did not want to limit
      * Inter-operable with sycl::event
      * Holds internal buffers, so keep track of lifetime. Attached to return value.
      
  *  Feedback

     * Do you have control over launching policy?

       * We use queue submit, so no control

     * Looks fine

       * Not sure adding dependencies is right, does not like argument number creep
       * _async is ok since return value is different

     * Could look like CUDA graph. Add .then.
     * Is this allowed to be eager?

       * Could start submitting at get
       * Probably best to allow it be eager without requiring it.

     * Can you re-submit the same graph?

       * You can create separate function, which addresses convenience
         but not performance
       * We are interested in looking at static graph
       * .then allows more explicit graph building
       * Looking at C++ executors, schedules, but proposals are not settled

         * It might address the issue of building/executing graphs

* Minimum C++

  * oneDPL supports C++11
  * SYCL 2020 requires C++17
  * Strong desire to move to c++17

  * Feedback

    * Kokkos moved to 14 in Jan and will move to 17 by end of year,
      stakeholders are ok
        
    * Surprises not good for users, should have very clear policy

      * e.g. support for latest-5 years
      * Established cadence
        
    * Is oneDPL useable without 17? Relying on sycl features which need it.

      * We have different set of execution policies


2021-3-24
=========

Attendees:

* Aksel Simon Alpay (Heidelberg University)
* James Brodman (Intel)
* John Melonakos (ArrayFire)
* Michael Kinsner (Intel)
* Alexey Kukanov (Intel)
* Nevin Liber (Argonne National Laboratory)
* Geoff Lowney (Intel)
* Greg Lueck (Intel)
* Andrew Lumsdaine (University of Washington, Pacific Northwest National Laboratory)
* John Pennycook (Intel)
* Pradeep Garigipati (ArrayFire)
* Pablo Reble (Intel)
* James Reinders (Intel)
* Alison Richards (Intel)
* Ronan Keryell (Xilinx)
* Roland Schulz (Intel)
* Gergana Slavova (Intel)
* Kevin Smith (Intel)
* Timmie Smith (Intel)
* Stefan Yurkevitch (ArrayFire)
* Xinmin Tian (Intel)
* Tom Deakin (University of Bristol)
* Umar Arshad (ArrayFire)
* Robert Cohn (Intel)

Opens
-----

* IWOCL and SYCLcon 2021 `registration is open <https://www.iwocl.org/>`__
* Our next TAB meeting (on April 28) will coincide with an IWOCL live event.
  Will shift our TAB meeting to 1 week earlier (to April 21).
* What other topics should we discuss here? Give us your suggestions.

SYCL 2020 implementation priorities (continued)
-----------------------------------------------

* Continued from `SYCL 2020 implementation priorities`_
* `Slides <presentations/2021-02-24-TAB-dpcpp-implementation-prioritization.pdf>`__
* No discussion on the following topics, please see slides for details.
  Special request to group: provide feedback on images as it hasn't gotten much attention
  in the community.

    * Kernel bundles
    * Specialization constants
    * Device copyable
    * Sampled_image, unsampled_image
    * Accessor to const T is read-only
    * sycl::exception error codes, not class hierarchy

* Implemented features

  * Kernels must be immutable
  
    * Change is due to high probability of bugs & allowing more freedom of implementation
    * A few folks have seen problems during implementation (when kernels could be mutable).
      Lots of discussion on how to define the right behavior so ultimately decided to restrict 
      mutability. If this group has use cases where restrictions need to be loosened, let the team know.
    * Do we need to add a note/block article to describe the issue? Yes, documentation is a good idea.
    
  * marray

    * vec used for SPMD code, but designed for SIMD (want to move in that direction in the future)
    * SIMD support via ESIMD, sycl::vec, std::simd
    * marray recommended for vectors in SPMD code

      * Size does not contain padding
      * No swizzle and write to element allowed

  * sycl::exception derives from std::exception
  
    * No discussion
    
  * Async errors no longer silently ignored
  
    * No discussion
    
  * sycl::bit_cast is c++20 bit_cast
  
    * No discussion
    
  * Queue

    * Without this, folks were missing a certain degree of control
    * Basically, a missing constructor: explicit context & device

  * Namespace from cl::sycl to sycl

    * Still accepts cl::sycl

* Looking forward to further input from this group on prioritization for LLVM open source project. Want to know:
  
  * What should be implemented next? What are you dependent on?
  * What's missing DPC++ that's critical for your workloads

* Request for additional features

  * Virtual function support

    * May not be possible on all devices, e.g. FPGA
    * FPGA has some workarounds when virtual functions are needed through std::variant

      * Is variant something we can use in the general case as well? No. 
        Requires developer to know all possible types & code is not easy to re-write 
	until you get pattern-matching.

  * Inheritance rules: single vs. multiple, restrictions

    * Could we use vtable size when conflicts arise?
    * OpenMP committee is considering limiting to single inheritance to make implementation easier
    

2021-2-24
=========

Attendees:

* Aksel Simon Alpay (Heidelberg University)
* David Beckingsale (Lawrence Livermore National Laboratory)
* Robert Cohn (Intel)
* James Brodman (Intel)
* Michael Kinsner (Intel)
* Alexey Kukanov (Intel)
* Nevin Liber (Argonne National Laboratory)
* Geoff Lowney (Intel)
* Greg Lueck (Intel)
* Andrew Lumsdaine (University of Washington, Pacific Northwest National Laboratory)
* John Pennycook (Intel)
* Pablo Reble (Intel)
* James Reinders (Intel)
* Roland Schulz (Intel)
* Gergana Slavova (Intel)
* Timmie Smith (Intel)
* Xinmin Tian (Intel)
* Tom Deakin (University of Bristol)
* Ronan Keryell (Xilinx)
* Alison Richards (Intel)
* Christian Trott (Sandia National Laboratory)
* John Melonakos (ArrayFire)
* Stefan Yurkevitch (ArrayFire)
* Umar Arshad (ArrayFire)
* Ruyman Reyes (Codeplay)
* Simon P Garcia de Gonzalo
* Pradeep Garigipati (ArrayFire)
* Andrew Richards (Codeplay)
  
SYCL 2020 implementation priorities
-----------------------------------

* `Slides <presentations/2021-02-24-TAB-dpcpp-implementation-prioritization.pdf>`__
* Need your feedback on prioritizing implementation of SYCL 2020
  features for upstream LLVM
* Atomics

  * Could AddressSpace argument be generated at runtime? Other implementations
    have not used it.
    
    * Perhaps can consider a basic version of atomic_ref without it
    
  * Limitations on arbitray size atomics? Do we need to go beyond 64?

    * Yes, need it to support complex double.

* Subgroups

  * How do we handle namespace changes and existing code?

    * We will have both for a period of time. Eventually DPC++
      extension will be deprecated.

* Group Algorithms

  * What are the restrictions on where you call the APIs, especially
    nested loops?

    * Designed to be called from ndrange parallel. Cannot be called in
      hierarchical parallelsim (parallel for work group, parallel for
      work item).

    * Could it work at work-group scope? We have it in hipSYCL.

    * Pennycook to follow-up offline

* Sub-group Algorithms: no discussion, check slides for details
* Reductions

  * Do you support multiple reductions? Limited support only. For example,
    no more than one reducer per kernel is allowed.

  * What happens if ndspan gets into C++23 but we are still on C++17?

    * Like span (C++20), we pre-adopt, eventually it becomes std::span

  * Why is parallel_for without explicit work-group size challenging?

    * Implementations have heuristics for work-group size. Can't use
      same heuristics because of other limitations: constraints on
      shared memory, etc.

  * Reduction code is 2/3 of the CUDA backend in Kokkos. It's important
    to have reductions in the standard - same code has failed by simply
    moving to a different version of the same hardware platform in the past.

  * Any performance testing with span reductions? Past experience has shown
    that performance falls apartn when going beyond 8, you are better off
    doing scalar.

  * Reductions aren't guaranteed to be deterministic? Right.

* Group Mask: no discussion, check slides for details
* Accessor Changes: no discussion, check slides for details
* Work-group local memory

  * What is the rationale for using a function instead of wrapper
    type? Similar feature in hipSYCL but implemented with wrapper.

    * Thread local was closest. Did not want keyword. Thought wrapper type was
      confusing for scope & visibility and has restrictions on where you can
      put it. Can't use as temporary. Looks like it is per work-item,
      but isn't.
    * We want to align on function vs. wrapper for next spec version
      (Roland will follow-up with Aksel)

* Multi_ptr: no discussion, check slides for details
* Heterogenous device

  * Is this a const expr function?

    * No. Only known at runtime.

  * Still looking at dispatching on the device, this is host dispatch.
  
* Did not finish the remainder - will bring this discussion back in March

  * Focused on describing items that are not fully implemented yet.
    Looking for prioritization from this group on what to do first.
    
* How should feedback be submitted?

  * Opening issues on `llvm github`_ is best. Ok to also use email to TAB members.
    
.. _`llvm github`: https://github.com/intel/llvm

2020-12-16
==========
Attendees:

* Alexey Kukanov (Intel)
* Gergana Slavova (Intel)
* Xinmin Tian (Intel)
* Sanjiv Shah (Intel)
* Andrew Lumsdaine (University of Washington, Pacific Northwest National Laboratory)
* James Reinders (Intel)
* Mark Hoemmen (Stellar Science)
* Piotr Luszczek (University of Tennessee, Knoxville)
* Christian Trott (Sandia National Laboratory)
* Nevin Liber (Argonne National Laboratory)
* Marius Cornea (Intel)
* Michael Kinsner (Intel)
* Edward Smyth (Numerical Algorithms Group (NAG))
* Sarah Knepper (Intel)
* James Brodman (Intel)
* Geoff Lowney (Intel)
* Pablo Reble (Intel)
* Mehdi Goli (Codeplay)
* John Pennycook (Intel)
* Roland Schulz (Intel)
* Timmie Smith (Intel)
* Shane Story (Intel)
* Maria Kraynyuk (Intel)
* Jeff Hammond (Intel)
* Nichols Romero (Argonne National Laboratory)
* Penporn Koanantakool (Google)
* Alison Richards (Intel)
* Robert Cohn (Intel)

oneAPI - how we got here, where are we going: Geoff Lowney

* `Slides <presentations/2020-12-16-TAB-oneAPI-year-one.pdf>`__

Small group discussions on 3 major themes identified in Geoff's presentation

* Irregular Parallelism: led by Mike Kinsner & James Brodman

  * Can we look to OpenMP? Mark up the work and later decide who does it.
  * Dynamic dispatch but need to consider:
  
    * Chicken and egg
    * Is this the right abstraction or is there a better option?
    * Is a kernel too much?
    * Do we need a smaller "task"?

  * Consider cross lane operations to help dynamically remap/move work. Do we need better
    ways to detect this?
  * Can cooperative groups help here? Is converged control flow restriction too limiting?
  * Tasking has been one approach
  
    * Granularity/complexity important - if it's too hard, an application might not use it


* NUMA: led by Xinmin Tian

  * `Slides <presentations/2020-12-16-TAB-DPCPP-NUMA-Discussion.pdf>`__
  * Places (an abstraction) is a reasonable abstraction for NUMA affinity control
  * The C++ standard committee executor WG is investigating NUMA support as well 
  * Ease-of-use considerations:
  
    * How to present NUMA control / usage model to users is very important for ease of use
    * A big customer prefers a simpler method for applications w.r.t NUMA domains usage.
      User expects implicit NUMA-aware support for applications cross-tile.
    * We may need high abstractions such as “spread” and “close” for programmers
    * Also need to support fine-level control for ninja programmers with a good mirror to architectural hierarchy
    * GPU (HW and driver) may support a “fixed mode” for programmers on NUMA thread-affinity control

  * Performance:
  
    * TensorFlow uses and supports a high-level control of NUMA domains for TF performance
    * Kokkos primarily uses OpenMP environment variables to get ~10x performance for some Kokkos users
    * Good thread-affinity control is tied to implementation specifics

  * Scheduling:
  
    * How to support NUMA control has impact on portability and scheduling. Explicit NUMA control is served better in applications.
      Use the subdevice (tile) as a GPU (a NUMA domain), then, the scheduling happens in the tile, which minimizes NUMA impact
      but is a bit more work for users.   
    * DPC++ (Gold) started with a high level control DPCPP_CPU_CU_AFFINITY={master | close | spread} for CPU.
      There are scheduling implications as well for thread-data affinity.
    * Need to give people an easy mode that works. Tying data to tasks is key: if we can design something where
      programmers say "Here are my data dependencies, please schedule this in a way that gets good performance"
      we'll have more luck than if we ask nonexperts to reason about things like whether pages should be interleaved
      and the granularity of thread scheduling.

* Distributed computing: led by Jeff Hammond

  * Preference for send-recv, particularly in stencil codes
  * TensorFlow doesn’t use MPI but we've reimplemented all of the MPI collective algorithms in MeshTensorFlow
  * What is the memory consistency model?  Assume memory consistency only at kernel boundaries.  We did
    distributed GPU in Kokkos already and it works great on DGX but may not apply in other cases.
  * Higher level abstractions are important but hard.  It’s nice to not have to implement the entire STL and start small.
  * Still upset at MPI standard dropping C++ bindings.
  * Getting things into ISO C++ is a huge pain.
  * MPI-3 RMA is amazing. Should we consider as similar model in DPC++?

2020-10-28
==========

Attendees:

* James Brodman (Intel)
* Robert Cohn (Intel)
* Tom Deakin (University of Bristol)
* Jeff Hammond (Intel)
* Ronan Keryell (Xilinx)
* Alexey Kukanov (Intel)
* Mike Kinsner (Intel)
* Jinpil Lee (RIKEN)
* Nevin Liber (Argonne National Laboratory)
* Geoff Lowney (Intel)
* Greg Lueck (Intel)
* Andrew Lumsdaine (University of Washington, Pacific Northwest National Laboratory)
* Heidi Poxon (HPE)
* Pablo Reble (Intel)
* James Reinders (James Reinders Consulting LLC)
* Alison Richards (Intel)
* Andrew Richards (Codeplay)
* Ruyman Reyes (Codeplay)
* Roland Schulz (Intel)
* Gergana Slavova (Intel)
* Timmie Smith (Intel)
* Christian Trott (Sandia National Laboratory)

SYCL/oneAPI 1.0 Spec Feedback: Roland Schulz, Michael Kinsner

* `Slides <presentations/2020-10-28-TAB-specFeedback.pdf>`__
* oneAPI spec 1.0 released on 2020-09-28; SYCL 2020 provisional released

  * Thanks to TAB for their ongoing engagement
  * Feedback provided has influenced both the DPC++ spec as well being fed into SYCL
  
* Specifically looking for directional feedback: items that are missing, that need more focus, or 
  are going in the wrong direction
* Extensions table in DPC++ spec section does not look up to date
    
  * oneAPI team to follow-up: e.g. SYCL provisional has parallel reduce but missing here
  * The more we can say: "this is just SYCL", the better

* Want to know occupancy of kernels

  * Need to add the ability to set the global and local range in parallel_for range
    not nd_range, and perhaps also to assert no barriers in nd_range parallel_for. 
    Would this be harder for CPU?
  * SYCL has mechanism for query, but what it queries is back-end
    specific - need to add something at the user level

* Better solution for trivially copyable issues

  * Everything you capture needs to be trivially copyable but implies
    destructor does not do anything specific
  * Unified shared memory (USM) is one way to deal with it but 
    it comes with penalties - need memcopyable solution
  * Example: a tuple is unlikely to be trivially copyable
  * Want the ability to have non-trivial destructors with byte-copyable objects
  * Need follow-up meeting: this time next week

* Static way to specify graphs of computations

  * After data movement is optimized, only thing left is latencies
  
    * Up to 40% latencies, in some cases
    
  * What about streams/events? They're not as effective as CUDA graphs.
  * Construct up front vs record/replay?

    * In Kokkos, it needs to be explicitly constructed
    * Having an explicit interface feels safer
    * Vulkan/cl have been looking at command lists
    
      * Level 0 has support for command lists
      
    * Some benefit for paramertizability
    * Would like to have timing of previous executions guide allocation/placement

* Auto-tuning for tiling/nd-range/work group size

  * Do I have to write heuristics for every platform when using oneAPI across GPU's/CPU's?
  * Kokkos has moved from heuristics to auto-tuning, including an auto feature where users let
    Kokkos choose parameters
  * Kernels can be called millions of time, auto-tuning in same run is
    not a big deal
  * Not just work group, also want to control occupancy: run at lower occupancy
    to use less cache. Could achieve 2.5x speedup by reducing occupancy.
  * Need a hint for parallel_for and query to know what happened
  * Want hints from the user about whether auto-tuning might be worthwhile

    * Building a graph is one hint
    * Hint about tuning parameter, does not change semantics, versus
      statements about barrier
    * Using property list
    * Lots of places where you hint

* Cooperative groups/barriers

  * Considering device barriers vs mpi-style
  * Kokkos is not using this because can't be sure it can be supported everywhere,
    and might not be faster than forcing a kernel stop/start. Latencies
    are also a problem and the device runs at lower frequency.
  * Going back to host is very expensive. Could we use wavefront algorithm?
  * Tried it for solvers, did not work
  * Prefer coarse-grain barriers because it is easier to support and barriers are just one among many sources of overhead

* How can we get more feedback on oneDPL, oneTBB?

  * Should we continue to discuss in this meeting or a separate forum?
    

2020-09-23
==========

Attendees:

* Robert Cohn (Intel)
* Gergana Slavova (Intel)
* Christian Trott (Sandia National Laboratory)
* Ruyman Reyes (Codeplay)
* Geoff Lowney (Intel)
* Heidi Poxon (HPE)
* James Brodman (Intel)
* James Reinders (James Reinders Consulting LLC)
* Mike Kinsner (Intel)
* Pablo Reble (Intel)
* Sergey Kozhukhov (Intel)
* Jinpil Lee (RIKEN)
* Timmie Smith (Intel)
* Ted Barragy (NAG Lead Computational Scientist supporting BP)
* Ronan Keryell (Xilinx)
* Roland Schulz (Intel)
* John Pennycook (Intel)
* David Beckingsale (Lawrence Livermore National Laboratory)
* Andrew Richards (Codeplay)
* Greg Lueck (Intel)
* Tom Deakin (University of Bristol)
  
Opens

* Welcome to Jinpil Lee who joins us from RIKEN! Jinpil is participating
  on the recommendation of Mitsuhisa Sato, RIKEN's deputy director.
* oneAPI spec v1.0 will be live next week. Thank you all in helping us
  achieve this tremendous milestone!

Extension naming: Greg Lueck

* `Slides <presentations/2020-09-23-TAB-Function-pointers.pdf>`__
* Purpose of this proposal is to prevent name conflicts between vendors
  extending the SYCL spec, and make the extension apparent in user code

  * Expect that SYCL new features will initially appear as extensions

* 3 options presented

  * Covers methods for macros, free functions, and members
  * Options took into account:

    * Verbosity
    * Similarity with past practice
    * Similarity to macro name when all caps is used

* Option 1: All capitals
* Options 2: Initial capital
* Options 3: EXT prefix
* Discussion

  * Option 3 preferred by multiple people. Reasons why:

    * Most consistent
    * Makes is clear this is an extension even if it's not obvious 
      based on the extension string
    * Any worry about additional verbosity?
    
      * Only 4 additional characters. Generally developers should be ok
        exchanging the extra characters for clarity.
      * More verbosity might be good here as it forces people to be deliberate
        when using extensions
      * For the vast majority, expect vendor-specific extensions to be temporary
        as they will be rolled into the standard. It is understood some may remain
	extensions forever because they are not suitable for standardization
	but those will be mostly exceptions.

  * Would like offline feedback on bad experiences with any of
    the options.
      
Function pointers: Sergey Kozhukhov

* `Slides <presentations/2020-09-23-TAB-Extension-Naming.pdf>`__
* Function pointers are important, we want to enable them in Intel
  implementation and SYCL spec
* The options shown are high-level summary of many detailed discussions - 
  mostly looking for feedback on the overall direction
* Today, function pointers are not allowed in device code, want to relax this restriction
* How are function pointers represented in source code? 2 options:

  * (Option 1) Implicit: typical C/C++ function pointers
  * (Option 2) Explicit: wrapper around pointer

* Many options exist for language and implementation:

  * Attributes vs wrappers
  * Part of function type

* Based on past experience with Intel compiler implementation:

  * OpenMP: attributes were enabled but not part of type system
  * Encountered difficulties in passing function pointers with different vector
    variants

* Option 1: use C/C++ function pointers

  * Every pointer is created with default set of variants: e.g. linear,
    uniform

* Discussion

  * Concerned about generating multiple variants. A lot of code
    generation. Is this really necessary, safe, clear how to implement
    with compilers?

    * Need it for virtual functions. Might need multiple variants for
      device.
    * CUDA has bare-boned function pointer. Only usable in the context
      where it is created (device, host).

      * We would still need translation functions for passing function
        pointers between host and device

    * This is for SIMD. Need to know: vectorization factor (subgroup
      size), mask/unmask. Writing SPMD, and want to use SIMD, need
      called function to be in vector factor/mask.

  * Compiler must create these variants and make choices as it compiles/builds
    binary, how portable is this between different compilers, different hardware?

    * Not an easy answer, also need to take ease of debugging into account - does it
      crash when it fails?
    * Each use case should be considered, including trade-offs for performance

  * Are attributes part of overload resolution? No.
  * Option 2 is really for non-virtual functions but overall direction might be to
    do a hybrid approach
  * Need more discussion on this topic. Bring back to October meeting.
  
    * Include more examples, clearer use case descriptions
    

2020-08-26
==========

Attendees:

* Robert Cohn (Intel)
* Gergana Slavova (Intel)
* Alison Richards (Intel)
* Andrew Richards (Codeplay)
* Ruyman Reyes (Codeplay)
* David Beckingsale (Lawrence Livermore National Laboratory)
* Geoff Lowney (Intel)
* Hal Finkel (Argonne National Laboratory)
* James Brodman (Intel)
* John Pennycook (Intel)
* Jeff Hammond (Intel)
* Roland Schulz (Intel)
* Ronan Keryell (Xilinx)
* Ted Barragy (NAG Lead Computational Scientist supporting BP)
* Timmie Smith (Intel)
* Tom Deakin (University of Bristol)
* Xinmin Tian (Intel)
* Andrew Lumsdaine (University of Washington, Pacific Northwest National Laboratory)
* Christian Trott (Sandia National Laboratory)
* Greg Lueck (Intel)
  
Opens

Spec: Robert Cohn

* Looking for feedback on usefulness of the `PDF version
  <https://spec.oneapi.com/versions/0.9/oneAPI-spec.pdf>`__ of oneAPI
  spec

Extensions Mechanism: Greg Lueck

* `Slides <presentations/2020-08-26-TAB-Extension-Mechanism.pdf>`__
* Extension mechanism

  * Discussion

    * Extension of existing classes breaks binary compatibility?

      * When moving between vendors, you have to recompile, even
        without extensions
      * It's the job of the implementor to ensure vendor-specific code
      	runs on targeted hardware

    * Needs more verbose guidance on how to make changes that are
      source compatible: conversions, constructors, overload sets.
      Further discussion to happen offline.

    * Compile-time warnings would be useful
      
* Optional features of devices

  * Similar to extension, because it may not be there
    
  * has() is passed aspect enum. Use if/template to handle absence of
    feature

  * Error handling

    * Throw runtime exception when using a feature that is not supported
      by device

  * devconstexpr: constant when compiling for device

    * Discussion
      
      * If this uses a keyword, it's no longer C++

	* Could hide it in macro but that has other downsides

      * Issues about lambda capture, device compiler, types not being
        present when feature is not supported.


Local memory allocation: John Pennycook

* `Slides <2020-08-26-TAB-LocalMemory.pdf>`__

* Ability to declare local memory for static size, instead of just accessor
* group_local_memory allocates, returning multi_ptr
* Discussion

  * Dynamically sized arrays

    * Only static, use accessor for dynamic

  * Support for arrays (std::array) and type requirements
    (e.g. trivially destructible)

    * Arrays supported, only requirement is trivially destructible

* Not enough time for full discussion, looking forward to further feedback here


2020-07-22
==========

Attendees:

* Robert Cohn (Intel)
* Gergana Slavova (Intel)
* Ilya Burylov (Intel)
* Alison Richards (Intel)
* Andrew Richards (Codeplay)
* Christian Trott (Sandia National Laboratory)
* David Beckingsale (Lawrence Livermore National Laboratory)
* Geoff Lowney (Intel)
* Hal Finkel (Argonne National Laboratory)
* James Brodman (Intel)
* John Pennycook (Intel)
* Mike Kinsner (Intel)
* James Reinders (James Reinders Consulting LLC)
* Jeff Hammond (Intel)
* Andrew Lumsdaine (University of Washington, Pacific Northwest National Laboratory)
* Roland Schulz (Intel)
* Ronan Keryell (Xilinx)
* Ruyman Reyes (Codeplay)
* Timmie Smith (Intel)
* Xinmin Tian (Intel)

Opens

Accessors: Ilya Burylov

* `Slides <presentations/2020-07-22 accessor simplification.pdf>`__
* Changes in accessors for SYCL 2020 provisional
* Device and host accessors have different behavior, not obvious from the call name

  * Absence of handler is interpreted different for host (blocking) and non-host (non-blocking) accessor
  * Placeholder host accessor are not supported
  * Considering making 2 new types of host accessor, blocking and non-blocking

  * Discussion

    * Concerns about excessive overloading and implicit behavior
    * Just call it non-blocking vs calling it a task

      * Names-based on semantics vs use-case
      * Recommend to make the code be self-descriptive

* Creating more dedicated types/alias

  * Is this level of granularity enough?

* Removed operator[](size_t index)

  * Allowed passing item instead
  * Need implicit conversions from size_t and other types to id
  
    * Should check spec that it works that way

* Feedback from Argonne

  * Highly desirable to have uniform set of rules for naming things

    * Define a consistent prefix
    * E.g. image_accessor vs host_image_accessor, should "image" always be first?

  * Deduction guides are useful, but don't solve the problem of strict argument order

    * Default arguments must be in order. Might be better to have specialized/more general.
    * Kokkos experience: helper classes take variadic arguments to make typedef

      * Host accessor does not help, because it needs to be stored and must be generic
      * Christian can provide an example to share with the group

  * Confusion around how local memory, irregularity around usage

    * Local memory allocated by accessor, different from all other accessors. Normally allocated somewhere else.
    * Difference between view & allocation
    * Working on a proposal, expect to bring it to this body for review soon

* Are 0 dimensional data structures used?

  * Yes, common in Kokkos

    * Atomic counters, error flags, ..

  * Would also like to see 0 dimensional buffer (no range, 1 element)
  * Need subspan mechanism to get view vs 1-off solutions
  
    

2020-07-01
==========

Attendees:

* Robert Cohn (Intel)
* Gergana Slavova (Intel)
* Alexey Kukanov (Intel)
* Antonio J. Peña (Barcelona Supercomputing Center)
* David Beckingsale (Lawrence Livermore National Laboratory)
* Geoff Lowney (Intel)
* Hal Finkel (Argonne National Laboratory)
* Heidi Poxon (HPE)
* James Brodman (Intel)
* John Pennycook (Intel)
* Roland Schulz (Intel)
* Ronan Keryell (Xilinx)
* Ruyman Reyes (Codeplay)
* Sandip Mandera (Intel)
* Timmie Smith (Intel)
* Tom Deakin (University of Bristol)
* Xinmin Tian (Intel)
* Alison Richards (Intel)
* Andrew Lumsdaine (University of Washington, Pacific Northwest National Laboratory)
* Andrew Richards (Codeplay)
  
Opens

* SYCL 2020 provisional spec is now public: James Brodman

  * Fairly major change vs. SYCL 1.2.1 including USM, quality-of-life
    improvements, new way of doing images
  * A lot of the changes included were prototyped in DPC++ first
  * Call for action: provide input on the spec either via the SYCL
    github (to be available soon) or through this group

* DPC++ vs SYCL

  * With SYCL 2020, differences between DPC++ and SYCL are smaller,
    would be good to see a technical list of differences
  * Would like to see a closer connection being made between DPC++ & SYCL
  
    * DPC++ messaging has explicitly shifted to highlight the fact that
      DPC++ = ISO C++ + SYCL + extensions
   
  * What is the need for a separate name, why not call it SYCL + vendor 
    extensions, similar to OpenMP?
  
    * DPC++ is a short-hand way to refer to the collection of extensions.
      While the difference between DPC++ & SYCL 2020 is fairly small now due to
      the recent release, expectation is to continue to prototype new extensions
      through DPC++ before upstreaming to SYCL.
  
  * This feedback will be rolled up to ensure it reaches the right people

Atomics: John Pennycook

* `Slides <presentations/2020-07-01-TAB-Atomics.pdf>`__

* deprecate cl::sycl::atomic replace with intel::atomic_ref

  * mostly aligned with c++2- std::atomic_ref
  * Which address spaces?

    * local, global, or generic

  * What about constant?

    * Atomic does not seem relevant
    * Issue about LLVM optimization, synchronization edges, etc. For
      more information, see comment 6 in `LLVM PR37716
      <https://bugs.llvm.org/show_bug.cgi?id=37716>`__

* memory orderings and scopes

* single happens-before relation

  * questions about hardware implications, need for fences
  * By specifying memory order/scope, you can tune performance
  * Situations where fences are required dominates the
    performance. Need to do the exercise where fences are required for
    common patterns and look at other architectures, if it will be
    part of SYCL

* changes to fences and barriers

* changes memory consistency model

  * makes sycl default behavior close to C++
  * difference still exists because private memory

* Questions

  * should we support std::atomic_ref in device code?

    * Yes as a migration solution, with expectation that eventually
      code uses SYCL native
    * Do not want to support name, but give it different meaning
    * Interesting to see if this supports different-sized <T>s

  * Do we need std::atom-like interface as well as atomic_ref?

    * Is the issue performance?

      * What are the semantics of std::atomic on host being
        accessed on device
      * Argonne has code that uses std::atomic. Would it make sense to
        compile code that uses it in device code?
      * what is code usage of std::atomic?

	* arrays, data structures


2020-05-27
==========

Attendees:

* Ted Barragy (NAG Lead Computational Scientist supporting BP)
* David Beckingsale (Lawrence Livermore National Laboratory)
* James Brodman (Intel)
* Robert Cohn (Intel)
* Tom Deakin (University of Bristol)
* Hal Finkel (Argonne National Laboratory)
* Ronan Keryell (Xilinx)
* Mike Kinsner (Intel)
* Alexey Kukanov (Intel)
* Geoff Lowney (Intel)
* Andrew Lumsdaine (University of Washington, Pacific Northwest National Laboratory)
* Antonio J. Peña (Barcelona Supercomputing Center)
* John Pennycook (Intel)
* Heidi Poxon (HPE)
* Pablo Reble (Intel)
* James Reinders (James Reinders Consulting LLC)
* Alison Richards (Intel)
* Andrew Richards (Codeplay)
* Roland Schulz (Intel)
* Gergana Slavova (Intel)
* Timmie Smith (Intel)
* Christian Trott (Sandia National Laboratory)

Data Parallel C++ Library continued: Alexey Kukanov

* `Slides <presentations/2020-05-oneDPL-for-TAB.pdf>`__

* Namespaces

  * oneapi:: vs one:
  
    * Don't like 'one': too much chance for collision
    * People will make jokes about 'one'
    * 'one' has poor searchability
    * People can make alias if they want something shorter
    
  * Board recommends 'oneapi'
  
* Top level include directory

  * one/dpl/ vs oneapi/dpl vs onedpl vs dpl
  * Board recommends to follow the namespace structure: oneapi/dpl
  * Can use symlinks/header that includes header for support old code
  
* oneDPL execution policy
* predefined execution policy

  * Verbose: default_policy cpu_policy, ...
  * Concise: cpu, gpu, default. Namespace will make it unique.
  * Don't like pol, preferred spell it out, default preferred to deflt
  * Generally concise is not preferred.  Code is read more than written so it's better to be verbose.
  * Like to distinguish between type and variable. Using C++17 std way with _v will make the distinction clear.
  * What about policy_gpu?
  
    * Not a big difference
    
  * Short names are not that short because you would normally have namespace
  
* Sync vs Async

  * Currently some algorithms block, some do not block
  * Board would prefer option 'c'
  
    * Standard API should be blocking
    * Add an explicit async API for those implementations that need it
    
  * For current implementation, move into namespace?  
  * No code out there now. Making it synchronous is a performance but not correctness issue. Like async, but if goal is to follow C++, then require all blocking
  
* Range-based API for algorithms

  * Allows concise expression of pipelines
  * Did we miss algorithms?  Please review list and provide feedback.
  * Add ranges now, or as extension/experimental?
  * Would be useful for graph library
  * No disagreement about delaying making it part of spec
  
    * Ok to have it implemented even though it's not part of spec.  No experience in HPC community with using ranges so having it available would give people a chance to experiment.
  
* Extension APIs

  * No discussion, see details in slide 14
 

2020-04-22
==========

Attendees:

* Bharat Agrawal (Ansys)
* David Beckingsale (Lawrence Livermore National Laboratory)
* James Brodman (Intel)
* Robert Cohn (Intel)
* Tom Deakin (University of Bristol)
* Hal Finkel (Argonne National Laboratory)
* Jeff Hammond (Intel)
* Mike Kinsner (Intel)
* Alexey Kukanov (Intel)
* Geoff Lowney (Intel)
* Antonio J. Peña (Barcelona Supercomputing Center)
* John Pennycook (Intel)
* Pablo Reble (Intel)
* James Reinders (James Reinders Consulting LLC)
* Ruyman Reyes (Codeplay)
* Andrew Richards
* Alison Richards (Intel)
* Gergana Slavova (Intel)
* Timmie Smith (Intel)
* Xinmin Tian (Intel)
* Phuong Vu (BP)

Notes:

* Administrative

  * `Rules of the road <presentations/oneAPI-TAB-Rules-of-the-Road.pdf>`__
  * Notes published immediately after the meeting on `Github
    <https://github.com/oneapi-src/oneAPI-tab/tree/master/tab-dpcpp-onedpl>`__
  * Email Robert.S.Cohn@intel.com or submit a github PR to add/remove name, add
    affiliation to attendees list

* Data Parallel C++ Library: Alexey Kukanov

  * `Slides <presentations/2020-04-22-oneDPL-for-TAB.pdf>`__
  
  * Recap
  
    * STL API
    * Parallel STL
    * non-standard API extensions
    
  * Required C++ version
  
    * Minimum DPC++ version will be C++17
    * Is it ok for oneDPL?
    * Will limit host-side environment. Default is C++14 for latest
      host compilers
    * Discussion:
    
      * Where are livermore compilers?
      
        * C++11 is fine, RAJA is C++11-based, some customers not ready
	  for C++14
	* What is the issue?
	
	  * People running on systems where supported gcc version is
            old
	  * But not about the code
      * Why is host compiler different?
      
      * If we require only 14, can we still make deduction work
        smoothly?
	
        * Yes
      * At Argonne, there is a range of conservatism, we should not
        impose artificial barriers
	
        * Provide C++17 features and ease of use when available, but
          there is value in being more conservative
	* On the other hand, we don't want to create 2 dialects
  * Top-level namespace
  
    * DPC++ has multiple namespaces: sycl::, sycl::intel
    * oneDPL adds a namespace
    * Discussion
    
      * Strictly standard could be nested, new things own namespace
      
        * Requires change to sycl spec
	
      * Standard allows to use the sycl::intel extension
      * Recommend top-level oneapi namespace
      
        * Can use C++ using to bring it into sycl::intel if desired
        * Example: oneapi::mkl
	
  * Standard library classes
  
    * Issues
    
      * Some classes cannot be fully supported
      * 3 different implementations
      
    * Options
    
      * White-listed
      * Freestanding implementation
      * Duplicate, bring standard library into SYCL
      
        * Spec says whether require implementation or to host to host
	
    * Analysis of pro/cons, see slide
    * Propose to go the combined route:
    
      * Whitelist the things that 'just work'
      * API's that need substantial adjustments are defined in SYCL spec
      * Freestanding for the rest
      * Analysis, see slide
      
    * Discussion
    
      * Seems like a practical solution
      * For freestanding, would there be conversions for standard types?
      
        * Yes
    * Slide shows mapping, whitelisted, custom, SYCL
    
      * Discussion
      
        * Functional can't be whitelisted
	
  * Not enough time for remaining topics, moved to next meeting
	  
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

  * Pointer-based memory management, complementary to SYCL buffers
  * What is the latency for pointer queries?

    * Have not measured, but it requires calls into driver and is not
      lightweight
    * Can it be accelerated with bit masks?
    * Could it be made fast enough so free() could check?

  * Are there any issues when using multiple GPUs?

    * All pointers must be in same context
    * Not likely to work if devices are not all from same vendor
    * Peer-to-peer, GPU's directly accessing each other's memory, is
      being considered for inclusion in Level Zero spec, and might be
      added to DPC++ spec
    * Non-restricted shared allocations should work fine

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
  
2019-11-17
==========

Slides:

* `Overview <presentations/2019-11-17-oneAPI-vision-for-TAB.pdf>`__
* `DPC++ <presentations/2019-11-17-dpcpp-language-and-extensions.pdf>`__
* `oneDPL <presentations/2019-11-17-oneDPL.pdf>`__


* What is oneAPI? 

  * oneAPI is a programming model for accelerators. It contains nine
    elements, in four distinct groups:

    * Language & its library
      
      * oneAPI Data Parallel C++ (DPC++) 
      * oneAPI Data Parallel C++ Library (oneDPL)
	
    * Deep Learning Libraries 

      * oneAPI Deep Neural Network Library (oneDNN) 
      * oneAPI Collective Communications Library (oneCCL) 

    * Domain-focused Libraries 

      * oneAPI Math Kernel Library (oneMKL) 
      * oneAPI Data Analytics Library (oneDAL) 
      * oneAPI Threading Building Blocks (oneTBB) 
      * oneAPI Video Processing Library (oneVPL) 

    * Hardware Interface Layer 

      * oneAPI Level Zero (Level Zero) 

* What is the minimum base language for DPC++?  Are newer standards
  supported? Have you talked about changing the DPC++ baseline to C++
  14?

  * C++11 is the base language for DPC++; more modern versions of C++
    can be used.  Our goal is to carefully define interoperability
    with features from newer C++ standards so that implementations of
    DPC++ are consistent.  (The Intel open source toolchain is based
    on trunk clang, so it is very modern.)
  * For SYCL the minimum base language is ISO C++11 (in SYCL
    1.2.1). C++11 features are used in the definition of language
    features.  This allows tools to compile SYCL even if they only
    support C++11.  Tools supporting newer C++ will compile code using
    newer C++ features, without issue.
  * Changing the baseline to C++14 is something that will happen
    shortly as part of the SYCL specification.  We expect to see a
    formal process and timeline defined that allows developers and
    implementers to reason about what the minimum version will be in
    future SYCL specifications.  And again, be aware that this is the
    minimum version which a compiler must support because mandatory
    language features use aspects of that C++ version.  Newer C++ can
    always be used if a toolchain supports it all that you lose is
    guaranteed compatibility with other implementations that don’t
    support as modern a C++ version.

* Why is the base OpenCL version 1.2 instead of 2.0?

  * OpenCL doesn’t have significant adoption beyond 1.2. The Khronos
    OpenCL working group is moving to a more flexible model, where
    only desired features beyond 1.x must be supported.  We’re
    aligning with that direction and want DPC++ to be deployable on a
    wide base of OpenCL implementations (which is 1.2 today).  DPC++
    features such as USM have OpenCL extensions to enable key features
    from DPC++ to be available on top of all OpenCL versions, such as
    1.2.

* The 0.5 specification has a table specifying which language features
  are required on a device and which are optional, e.g.,
  pipes/channels are required on FPGA and subgroups not required on
  FPGA. How did you make this decision?

  * Most features should be supported on all devices for functional
    portability, even if not performant.  However, some language
    features are naturally IP specific.

  * Pipes are an easy example.  Pipes are designed for spatial
    architectures and require independent forward progress across
    kernels for many uses, a forward progress guarantee that we don’t
    want to impose on all devices.  OpenCL 2.0 tried to make pipes
    usable on GPUs as well as FPGAs and ended up with a bloated
    feature that nobody uses because it can’t achieve performance
    anywhere, even on FPGA.

  * Implementation effort is also a consideration.  We don’t want to
    create large additional effort in DPC++ implementations for a
    feature on an IP where it is expensive to implement and will
    rarely be used.  We see a balance between requiring implementation
    effort vs portability of a feature across all devices.

  * Subgroups are not required on FPGA, because implementations
    typically do not vectorize across work-items.  However, subgroups
    can be easily implemented with a subgroup size of 1.  Would this
    be a useful change to the specification?

* Unified Shared Memory (USM) how does this work with OpenCL?

  * We have published the appropriate extensions for OpenCL to enable
    USM.  USM should be considered an alternative to (or a replacement
    for) the SVM features added to OpenCL 2.0, with USM being designed
    to be much more usable.  Note our proposed OpenCL USM extension
    builds on top of even older OpenCL versions.

* Directed Acyclic Graphs (DAGs) buffers/accessors allow creation of
  implicit DAG edges. However, this feature does not interact well
  with C++ classes. Will DAGs independent of buffers be added, for
  better C++ support/integration?

  * The USM extension adds an explicit “depends on” mechanism, for DAG
    edge creation without buffers/accessors.  Please give us feedback
    if you want tweaks or different interfaces for specific use cases.

* Will USM replace OpenSHMEM? 

  * No. USM is currently defined within a single node, whereas
    OpenSHMEM is a scale-out model for distributed memory. We believe
    OpenSHMEM and USM are independent and expect both to work
    together.

  * In terms of the mental model for USM vs SYCL buffers, it is a bit
    like a PGAS language (e.g. UPC) vs MPI because USM supports
    load-store between different physical address spaces, whereas SYCL
    buffers are opaque objects, but one does need to understand MPI or
    PGAS to program in SYCL.

* Do the USM allocator functions permit the definition of new allocators? 

  * Yes, it is possible to define your own memory allocation model.
    That is hidden in “…” in the slides - there is a C++ allocator
    interface.  The USM extension defines a variety of mechanisms for
    allocation.

* Do the USM allocator functions permit the definition of new
  allocators?

  * Yes, it is possible to define your own memory allocation model.
    That is hidden in “…” in the slides - there is a C++ allocator
    interface.  The USM extension defines a variety of mechanisms for
    allocation.

* Reductions 

  * Motivation.  Reductions are foundational for parallel processing;
    users should not need to write out the details of their
    implementation. The compiler team should do a very good job of
    optimizing the reduction call based on target architecture. A
    bunch of physicists and chemists should not have to do this to run
    molecular dynamics. It needs to be provided in the language; most
    programmers will call SYCL reduce and be happy. The proposed DPC++
    extension will be proposed to Khronos as an extension to the SYCL
    standard.

  * Determinism.  With floating point arithmetic, deterministic
    reductions can be very expensive.  We chose not to define
    determinism or ordering in this version, but we would like to know
    what specific requirements you have. We believe that both
    non-deterministic and deterministic reductions have a place and
    need to be enabled.  We’ve started with non-deterministic because
    they cover many uses and are much more performant on some
    hardware.

    * It is OK for default to be non-deterministic but also want the
      ability to set a runtime flag and have determinism if required.
      This should be set on a per reduction/per kernel-level, not
      globally.
 

    * The specification shouldn’t over specify.  In specific (not all)
      cases I want to have determinism.

  * Hardware issues.  On the Intel GPU, we have 3 levels of reduction:
    EU level reduction, SLM level reduction, global reduction. We need
    to be careful and think about how the language level reduction
    will map to HW for both non-deterministic and deterministic
    reduction.

    * If you want this to be an industry specification you must be
      very careful DON’T THINK OF INTEL HW think of any possible
      hardware available.

  * Compiler issues.  How can the compiler support multiple devices
    efficiently?  You can have only one SYCL application.  How can you
    know it’s going to run on a FPGA or on what HW?  How do you get it
    to run best on the HW?

    * Some flows create outputs for multiple targets, known at compile
      time.  These implementations will be specialized. SPIR-V for
      generic targets requires a generic implementation, unless these
      primitives are defined through SPIR-V. The fat binary direct
      specialization flow is expected for performance. Should library
      calls for reduction be defined at the SPIR-V level?

  * Parallel reduce or Parallel For.  Don't like that you are
    doing parallel_for with a reduction clause…  There is a reason
    that TBB has reduce.  Why are you making a different choice?

    * We are treating this in the same way as collectives there are
      several collectives that operate on multiple work items that are
      running.  Treat reduction as across the iteration space.

    * Can we make a language distinction between loops with completely
      independent iterations and ones with some type of dependencies?
      How can we distinguish between the two?  That would be useful.
      Then the reduction question becomes more salient never call a
      synchronization across work groups.

    * We should have a broadcast primitive.  You want reduction plus
      broadcast.

* Standardization efforts work well when there is enough experience
  and the effort can be focused on standardizing best practices.  Are
  we at this point or are their fundamental unresolved issues?

  * Consider the MPI forum work.  Everyone knew how to do proper
    message passing just an issue of setting an API.

  * MPI2 RMA is not so good… don’t want that.  I started doing an
    industry wide study of data parallelism and went through TBB,
    Kokkos, RAJA, and then stumbled on SYCL.  There are important
    questions but with DPC++ we are at a similar level of experience
    to MPI1 message passing systems on supercomputers.  This is meant
    to be iterative, not converge on one true solution immediately.
    These are mostly syntax debates Kokkos vs Raja syntax debates.

  * This discussion is a core reason to have iteration with respect to
    DPC++ extensions.

* Is the kernel argument restrict for USM pointers or buffers?

  * Both. 

* Optional Lambda naming 

  * Required lambda naming causes a variety of problems, particularly
    for libraries.  The Intel open source DPC++ implementation has had
    optional lambda naming for a while now.

  * Lambda names are very useful for debugging and profiling.  Give it
    a string as a profiling.  Names are optional, but still a type.
    Request for:

    * Need to have a string-based name AND

    * We should add the option to have string names on buffers - look
      at Kokkos as example

* Other implementations - How can you make this more attractive for
  your competitors to adopt this? Some of us have spent years
  developing OpenCL code due to vendor-independence and
  portability. Will look to see if DPC++ gets adopted by other
  vendors.

  * Codeplay has announced they will support DPC++ on top of Nvidia
    hardware. See article here.

* What is oneAPI?  What is DPC++?  What is SYCL? 

  * oneAPI is the programming model, consisting of a language, a set
    of libraries and an HW interface layer.

  * DPC++ is the language, built on ISO C++ and Khronos SYCL and
    extensions.

  * Some think of oneAPI as the platform, and DPC++ as the language
    built on C++ and SYCL.  Most of the extensions that form DPC++ are
    being fed back into SYCL for consideration and hopefully inclusion
    in future standards.

* Really like what you are saying, however DPC++ could be perceived as
  “pulling an OpenACC”. Why not just call it SYCL?

  * We are aware of that possible misperception. We want to be very
    explicit about how we are different than OpenACC versus OpenMP:

    * We are not forking from SYCL, we are building on top of it.  

    * We are very explicit that DPC++ == ISO C++ and Khronos SYCL and Extensions 

    * We are discussing all our extensions openly with the SYCL committee. 

    * We are not forming another standards foundation/group.  

    * We are being very open, using permissive licensing and an open implementation 

    * The collective set of extensions does need a name.

    * We are working with both Khronos SYCL and ISO C++ to put as many
      of these extensions into those standards as possible. That will
      take time and we will continue to work on it.

    * We intend to make the codesign process with our customers much
      faster than is possible otherwise

* What does STL vector container mean in the context of accelerator? 

  * Ideally, we want to get the full STL working, however as you note,
    we know there are challenges. For example, a parallel push on
    vector is problematic. We may allow some operations but not all.

  * Need to worry about pointer, shared pointer, and container
    semantics.

  * Push in a parallel context?  A lot of C++ was not made for
    parallelism.

  * Simple acts: pointers, iterators on top of that…

  * Two high-level things:

    * What do we expect to support for device-side memory allocation?
    * Can I free it on the host or on the device?  A lot of uses where
      we have code paths to do that (particle codes, etc.) But you
      don’t want to build something like vector push-back.  You want
      to allocate in chunks. How you build that in?  What primitive
      do you want to provide in a parallel construct.  Don’t pick the
      convenient thing to do… you are making a standard so think
      about it and how you want this be careful and offer what will
      work over time.

* Capturing objects in a lambda does USM guarantee that you have a
  coherent connection between host/accelerator?

  * No 

* What about Python, Java, C#? Will those be part of the oneAPI effort
  in the future?

  * Our thought process is to focus on the lower levels of the stack
    and allow others to build on it. We do not want to push into
    higher levels of the stack it is a large space. Instead, we want
    to offer an open specification, in open source, and provide
    infrastructure that others can build upon. Some examples: 

    * with our LLVM work, we hope to allow anybody to build additional
      languages that can easily by powered by oneAPI and add
      accelerator support. An LLVM-based language like Julia could
      easily leverage this work to support any oneAPI platform

    * The hardware interface layer, Level Zero, could be used by any
      language if so desired.

    * Level Zero could also be implemented by any HW vendor to
      leverage the entire oneAPI SW stack.

    * We will plug oneDNN and oneCCL into deep learning
      frameworks. This could then enable any HW vendor to implement
      oneDNN and oneCCL to plug into all frameworks instead of
      building framework-specific interfaces

    * We will plumb the oneAPI libraries into the Python ecosystem via
      numpy, scipy, scikit-learn, pandas interfaces.

    * The Python numba compiler could leverage the LLVM infrastructure
      to enable accelerator support.

* USM vs buffers 

  * There are a few other reasons why buffers allow you to work out
    the memory model.  Note OpenCL only gives you buffers.  Buffers
    allow the accelerators to know what they need to work on.  You may
    be able to create an accelerator that doesn’t use pointers but may
    use a DMA system.

  * I can see why people want USM but mixing USM w/ buffers may not
    make sense.  It may be better us use buffer with indices into
    arrays to build data structures.

* Data migration with USM 

  * Is there an interface that will allow you to do on-demand paging?
    Will you be able to adapt to where the data is?  If it’s on the
    GPU, run on the GPU; if it is on the CPU, run on the CPU.

  * C++ had no notion of this without NUMA.

* Other general comments

  * Like that you are getting feedback on github.

 

 

 
