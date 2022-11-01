================================================================
oneAPI Community Forum Combined Meeting Notes
================================================================

2021-12-14
==========

Agenda
------

=========================  ============================================
Introduction               Geoff Lowney
Unified Runtime            Paul Petersen, Z. Waters, A. Kukanov, T. Smith
Distributed Programming    David Ozog, Maria Gazaran, Robert Cohn
Domain Specific Libraries  Ilya Burylov
=========================  ============================================


Attendees
---------

=================================   ===============================
Tian, Xinmin, Intel                 Lowney, Geoff, Intel
Slavova, Gergana S, Intel           Richards, Alison, Intel
Smith, Timmie, Intel                Podogova, Svetlana, Intel
Brodman, James, Intel               Ashbaugh, Ben, Intel
Lueck, Gregory M, Intel             Garland, Craig, Intel
SBaik, Samsung (게스트)              Burylov, Ilya, Intel
Garzaran, Maria, Intel              Sohrab Amirghodsi, Adobe
Ozog, David M, Intel                Kawakami, Kentaro/川上 健太, Fujitsu Labs
Cohn, Robert, Intel                 Pat Quillen, Math Works
Edward Smyth, NAG                   Ike, Atsushi/池 敦, Fujitsu
Luo, Ye, ANL                        Mehdi Goli, Codeplay
Tom Deakin, Univ of Bristol         Bharat Agrawal, Ansys
Khanna, Rahul, Intel                Rabotnikov, Mark, Philips Medical System
Knepper, Sarah, Intel               Waters, Zack S, Intel
Story, Shane, Intel                 Kharkov, Egor, Intel
Kukanov, Alexey, Intel              Kubarev, Valentin, Intel
Pascuzzi, Vincent, BNL              Petersen, Paul, Intel
Luszczek, Piotr Rafal, UTK          Stefan Yurkevitch, ArrayFire
Schneider, Robert, Seimens HC       Ruyman Reyes, Codeplay
Pennycook, John, Intel              Andrew Lumsdaine, University of Washington
Arunachalam, Meena, Intel           John Melonakos, ArrayFire
Patty, Spencer, Intel               Reinders, James, Intel
Dolbeau, Romain, SiPearl            Li, Jian Hui, Intel
Rahman, Md, Intel                   Sheng Zha, AWS MXNET
Nevin Liber, Argonne                Alastair Murray, Codeplay
Paul, Sriraj, Intel                 Andrew Richards, Codeplay
Anzt, Hartwig, KIT                  Ronan Keryell, Xilinx
Shabunin, Maksim, Intel             Erik Lindahl, Stockholm University
=================================   ===============================

Slides_

.. _Slides: presentations/cross-tab-2021-12-14.pdf

* Adobe only uses native runtime on each platform: Metal or Direct X

* Need Vulcan back end support – requires more image processing, which
  is missing from oneAPI – prefers to write in oneAPI in C++ vs
  GLSL/etc. Needs texture support, image basic support, access to how
  a texture is stored (linearly or swizzled, 3D texture or a view on
  that with two images, etc)

  * Working on imaging support for oneAPI.

  * AR: Zack to connect with Ilya on image support

* Does Level Zero support OpenCL?

  * Level Zero provides low level access to GPU and devices, and is
    intended to be lower-level than OpenCL. OpenCL is a true citizen.

* We are doing a lot of work in SYCL and NVIDIA Backend.  Very
  interested in level zero – looked at level zero but it was so low
  level compared to CUDA and Hip; Need something higher level to
  implement the sycl support and support other platforms above CUDA
  and HIP – need other ways to do that; if you think in terms of
  SYCL. How would they do that with multiple streams in cuda and or
  multiple queues.  A lot of conversations to have a unified RT for
  other platforms.

  * This is a .1 spec, would like to get additional engagement for
    others; in terms of a timeline, is there something we could share?
    We need to put the draft on the github – we have the AR to put
    that up.  We do intend to have an easy migration path – for other
    hw.  Clean up and standardize in the PI layer – we welcome your
    feedback as we define.  On the right track w/ the unified RT; one
    of the nice things through the adaptors is the ability to expose
    the extensions and expose the native platform experiences – if you
    need access for CUDA and low-level interfaces or APIs, you could
    be able to choose

  * SYCL for CUDA or for HIP – is the backend to call cublas directly,
    very important feature that they need – they are happy to
    collaborate on this (Codeplay).

* Issues with level zero

  * Unified runtime expected to abstract all the backends but they are
    more familiar with other APIs.

  * Thread Safety – SPEC says level zero is not thread safe.  (ZACK – AR
    to look at that).  Spec says you can call for multiple threads.  2)

  * No primary context to allow libraries to interact w/o knowing each
    other; user exports (unfriendly for developers).  Need to have a
    Level Zero adaptor to unify the behavior w/ other runtimes.  That
    is more of his expectation.

  * Adaptors – where you need divergence, you can access the lower
    level platform…  Need to access multiple lower level architectures
    – directly allocated from cublas or cuda low level (goes through
    the primary context and recognizes each other – independent but
    not convenient like that w/ CUDA).  Unified RT is the opportunity
    for supporting those explicit constructs.

  * You can call from multiple threads but you need to be
    careful. Can't operate on the same object from multiple threads.
    Small clarification: You can't operate on the same object from
    multiple threads without synchronization.

  * Having this TLS magical CUDA state is a limitation for
    performance; And it is actually very non-thread safe, just in a
    more dangerous and subtle way. It also creates wrong expectation
    to users, e.g. CUDA libraries magically working without
    initialization! so when you need to interop with native libraries,
    you need to explain to users all the magic that CUDA runtime does
    and how to replicate it in a portable way

* TLS will create more issues than the benefits we will get. It is a
  legacy issue in OpenMP now.

* But this does not prevent to provide an optional TLS layer for
  porting simple use-cases coming from a single-thread world ? Just
  that it should not be the default and uninterested folks should not
  be performance-impacted from this.

* Do people want to program CPU with SYCL?

  * Yes

* Would resource manager help TBB and NUMA issues on Intel CPUs?

  * That is exactly why we are pursuing this.

* We need to support both 32 bit (WASM) and 64 bit system. From high
  end workstation to iOS/Android devices. So portability and ability
  to scale down gracefully is critical for us. Apple platform support
  only their own solutions and we have had lots of issues with OpenCL
  on random hardware with OpenCL drivers. For cloud computing we have
  more flexibility.

* MPI could surely benefit from modern C++ bindings....

* Do you still rely on free functions in shmem? We are back into our
  previous TLS discussion. Perhaps using kernel handler would be more
  C++ & SYCL compliant? Of course the syntax would be different, which
  is a problem for portability, with kh.shmem.putmem_nbi() for example
  instead of ::shmem_putmem_nbi(() –

* Do we need to be looking at different extensions?  Do we need this
  natively in SYCL?  Ie. Universal Parallel C++ (Paul).  How do we
  think about this (not in a library point of view but have this more
  integrated w/ SYCL)

* For combining MPI and SYCL, have you looked at the Celerity project?
  https://celerity.github.io/ Celerity · High-level C++ for
  Accelerator Clusters High-level C++ for Accelerator Clusters

* [Off-topic; Potential Collab] I have yet to see 'XPU' with 'X' ==
  'Q'. Something I'm interested in is having a qpu_selector, where
  this would use a QC simulator (akin to, e.g., an FPGA simulator) for
  Qiskit, cirq, DM-SIM, etc., perhaps via PI interface? Feel free to
  reach out.
