======================================================================
oneAPI Technical Advisory Board Meeting (DPC++ & oneDPL) Meeting Notes
======================================================================

2020-04-22
==========

Attendees:

* Bharat Agrawal
* David Beckingsale
* James Brodman (Intel)
* Robert Cohn (Intel)
* Tom Deakin (University of Bristol)
* Hal Finkel
* Jeff Hammond (Intel)
* Mike Kinsner (Intel)
* Alexey Kukanov (Intel)
* Geoff Lowney (Intel)
* Antonio J. Peña (Barcelona Supercomputing Center)
* John Pennycook (Intel)
* Pablo Reble (Intel)
* James Reinders
* Ruyman Reyes
* Alison Richards (Intel)
* Andrew Richards
* Gergana Slavova (Intel)
* Timmie Smith (Intel)
* Xinmin Tian (Intel)
* Phong Vu (BP)

Notes:

* Administrative

  * `Rules of the road <presentations/oneAPI-TAB-Rules-of-the-Road.pdf>`__
  * Notes published immediately after the meeting on `Github
    <https://github.com/oneapi-src/oneAPI-tab/tree/master/tab-dpcpp-onedpl>`__
  * Email Robert.S.Cohn@intel.com or github PR to add/remove name, add
    affiliation to attendees list

* Data Parallel C++ Library: Alexey Kukanov

  * `Slides <presentations/2020-04-22-oneDPL-for-TAB.pdf>`__
  * Recap
  
    * STL API
    * Parallel STL
    * non-standard API extensions
    
  * Required C++ version
  
    * DPC++ will be C++17
    * Is it ok for oneDPL?
    * Will limit host-side environment. Default is C++14 for latest
      host compilers
    * Discussion:
    
      * Where are livermore compilers?
      
        * C++11 is fine, Raja is C++11-based, customers not ready to
          C++14
	* What is the issue?
	
	  * People running on systems where supported gcc version are
            old
	  * But not about the code
	  
      * Why is host compiler different?
      * If we require only 14, Can we still make deduction work
        smoothly?
      
        * Yes
	 
      * At Argonne, there is a range of conservatism, we should not
        impose artificial barriers
      
        * provide c++17 features and ease of use when availabe, but
          value in being more conservative
	* don't want to create 2 dialects
	
  * Top-level namespace
  
    * DPC++ has multiple namespaces: sycl::, sycl::intel, std::
    * DPL adds a namespace
    * Discussion
    
      * strictly standard could be nested, new things own namespace
      
        * requires change to sycl spec

      * standard library could be part of standard
      * standard allows extension sycl::intel
      * like top-level oneapi namespace
      
        * can use c++ using to bring it into sycl::intel if desired

      * oneapi::mkl
      
  * standard library classes
  
    * issues
    
      * some classes cannot be fully supported
      * 3 different implementations
      
    * options
    
      * white-listed
      * freestanding implementation
      * duplicate, bring standard library into SYCL
      
        * spec says whether require implementation or to host to host
	
    * analysis of pro/cons, see slide
    * Proposed combined
    
      * whitelist the things that 'just work'
      * API's that need substantial adjustments are defined in SYCL spec
      * freestanding for the rest
      * analysis, see slide
      
    * Discussion
    
      * Seems like a practical solution
      * for freestanding, would there be conversions for standard types?
      
        * Yes
	
    * Slide shows mapping, whitelisted, custom, SYCL
    
      * Discussion
      
        * functional can't be whitelisted
	  
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
  
2019-11-17
==========

Slides:

* `Overview <presentations/2019-11-17-oneAPI-vision-for-TAB.pdf>`__
* `DPC++ <presentations/2019-11-17-dpcpp-language-and-extensions.pdf>`__
* `DPL <presentations/2019-11-17-oneDPL.pdf>`__


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

 

 

 
