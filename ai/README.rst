===================================
oneAPI Community Forum AI SIG
===================================

The AI SIG hosts discussions and presentations focused on
AI operations and interfaces. The oneAPI specification
defines the oneDNN interface with building blocks for
deep learning applications and frameworks.

The AI SIG is led by Penporn Koanantakool (Google) and Jian Hui Lim (Intel).

To find out how to join the AI SIG `get in touch`_.

.. _`get in touch`: https://www.oneapi.io/community

Meeting Notes
=============

2023-06-28
==========

`Meeting notes <presentations/2023-06-28-JAX-PJRT-Intel-GPU-meeting-note.pdf>`__

`Accelerate JAX on Intel GPUs via PJRT <presentations/2023-06-28-JAX-PJRT-Intel-GPU.pdf>`__

2023-03-15
==========

`Meeting notes <presentations/meeting-notes-2023-03-15.pdf>`__

`Hugging face <presentations/Hugginface_Intel_Model_Opti_Julien_Simon_Matrix_Yao_2023_3_15.pdf>`__

`Joint Matrix <presentations/Joint_Matrix_Dounia_Khaldi_2023_3_15.pdf>`__

2022-07-14
==========

Agenda
------

===================  ===============================
oneDNN v2.0          Mourad Gouciem, Intel
oneDNN Graph API     Jian Hui Li, Intel
===================  ===============================

Attendees
---------

==============================  ==============================
Robert Cohn, Intel              Ramtin Davanlou, Accenture
Mourad Gouciem, Intel           Rajeev Nalawadi, Intel
Guolian Li,                     Tamir Guy, Intel
Rahul Khanna, Intel             Andrew Chen, Vastai Tech
Cheol Kim, Intel                Jason Wang
Jian Hui Li Intel               Mehdi Goli, Codeplay
Penporn Koanantakool, Google    Tong Gu, Intel
Stephano, RISC-V                Henry Gabb, Intel
==============================  ==============================



Slides
------

`oneDNN <presentations/oneDNN-2022-07-14.pdf>`__

oneDNN v2.0
-----------

* Intro

  * Improved support for int8 quantization
  * Better runtime dimension support

* Quantization

  * Per tensor quantization to allow asymmetric, dynamic quantization

* Relaxed math mode

  * allow low precision without changing code
  * user sets minimal datatype, control

* API stability for developers

  * descriptors are transparent
  * exposes implementation n API
  * extensions break compatibility

* Making runtime dimensions support more practical

  * current spec has runtime dimensions
  * scratchpad allocated at creation time before dimensions are known
  * user provides allocator. Allocated at runtime

* Misc

Q: How does allocator know which device, numa node to use?
A: Allocator called by thread executing on numa node, pass in device

oneDNN Graph API
----------------

* oneDNN Graph API

  * Currently oneDNN supports primitives API, will add graph API for future.
  * Graph API allows HW backend to max performance.
  * Same integration for multiple AI HW: CPU, GPU, and accelerators.
  * More flexible/productive way to add fusions compared to strict
    library API
  * In pytorch 1.2 release
  * In upcoming oneDNN 3.0 product release, oneDNN 2.0 spec release

* Relationship between primitive and Graph APIs

  * 6 graph api classes: tensor, logical tensor, op, graph, partition, compiled partition.
  * share stream/engine with rest of onednn

* Framework integration

  * before graph api

    * Framework integration needs to pattern match for oneDNN primitives
    * Each fused op needs to register to framework runtime and extend pattern matcher

  * with graph api

    * There is no need for differentiate which op etc. and no need to
      modify framework for new fusion.

* Has larger scope for better efficiency

  * Fusion avoid loading from memory
  * Can change representation/types inside fused ops

Q: Can graph be executed on multiple stream/engine

   Graph bound to a single stream

Q: What happens when oneDNN graph cannot support compilation
A: Framework integration responsible for executing

Q: How does this interact with framework graph rewrites?

   Expect that target independent rewrites are performed before oneDNN
   graph. Difficult when framework performance fusions.

Q: What happens when using CPU + nvidia GPU together? Can't use oneDNN
   for graph that will be executed on GPU. Need cost model to decide
   what to send to oneDNN graph

   Do per-device partition

Future topics
-------------

Suggestions for future from TAB members:

Plans for Habana synapse API?

Comparison of GPU vs CPU. Facilitating migration between CPU &
GPU. What is well suited.

Accenture open source reference kits for AI, using oneAPI components.

2022-3-8
========

Agenda
------

================================================================  ===============================
Interfacing oneAPI and Python                                     Diptorup Deb, Intel
Metagraph Project                                                 Stan Seibert, Anaconda
================================================================  ===============================

Attendees
---------

=================================   ===============================
Radionov, Alexander, Intel          Khanna, Rahul, Intel
Pavlyk, Oleksandr, Intel            Voss, Michael J, Intel
Richards, Alison L, Intel           Arunachalam, Meena, Intel
Deb, Diptorup, Intel                Andrew Chen, Vastai Tech
Ruyman Reyes, Codeplay              Li, Jian Hui, Intel
Brodman, James, Intel               Nalawadi, Rajeev K, Intel
Cave, Vincent, Intel                Cheng H. Lee, Anaconda
Mehdi Goli, Codeplay                Andrew Richards, Codeplay
Romain Dolbeau, SiPearl             Penporn Koanantakool, Google
Tamir, Guy, Intel                   Davanlou, Ramtin, Accenture
Gabb, Henry A, Intel                Stan Seibert, Anaconda
=================================   ===============================

Slides
------

`Intro <presentations/oneAPI%20AI%20TAB%20intro%20March%208%202022.pdf>`__

`Interfacing oneAPI and Python <presentations/Data-Parallel-Essentials-For-Python-oneAPI-TAB.pdf>`__

`Metagraph <presentations/2022_03_Metagraph_v1.pdf>`__

Interfacing oneAPI and Python
-----------------------------

Q: Why did you not use Buffers?

   While it is technically possible, different Python classes would
   need to be created for every supported buffer data type as the
   buffer and accessor type definitions require the type of the
   underlying elements. We can get around the issue by using “untyped”
   buffers, but that brings its own challenges as partitioning of
   buffers can lead to loss of precision and incorrect results.

Q: Using SPIR V – and using SYCL as the API, is that easier for
   interoperability.  Why not use Open Cl?  Or go straight down to
   Level Zero of oneAPI?

   We envision a DPC++ program manager like layer in Numba that will
   allow us to go from the same high-level Python code to possibly
   different types of IRs (SPIR V, NVPTX) and then build
   interoperability kernels that can be launched using a SYCL
   runtime. Targeting OpenCL or Level Zero restricts us to devices
   that support Level Zero. The design may change later as the system
   evolves.

Q: Using MLIR as well – but you have SPIR V at the bottom?  Using MLIR and SPIR V at the bottom?  Code level?

   The MLIR GPU and SPIR V dialects offer greater flexibility to us
   than Numba’s current pipeline. We want to move away from using the
   llvm-spirv translator and hope that the GPU dialect grows into
   support other types of devices not just GPUs.

Q: Codeplay has done work on MLIR.  Would like to connect SYCL dialect and want to focus on top half of the box (SPIR V – GPU- Slide12)

   For the Python work we want to primarily focus on the Python to
   Optimized loops pipeline. If the community takes over the SPIR-V
   and GPU (and possibly a SYCL dialect), our work for the Python
   compiler will be greatly benefit.

Q: What does it mean to make python code look more like SYCL?

   Do as a community effort – Anaconda may have responses – will need
   to involve the NVIDIA engineers who work on Numba?

Q: SYCL Dialect in the future?  Do we have a timeline for that?

   SYCL dialect doesn’t exist right now. I am not aware of any
   timeline, or if anyone is working on it.

Q: Runtime – how much overhead is there from the Python layer?

   Library call – oneMKL interface layer – there is not much overhead
   – did not observe – better than 90%; for the compiler, also we have
   been evaluating the code we generate through NUMBA DPEX – 75-80% of
   the execution time as compared to DPC++

Metagraph
---------

Q: Graph Neural Net – is it flexible enough for a graph?

   https://blog.tensorflow.org/2021/11/introducing-tensorflow-gnn.html

Q: Big fan of Graph BLAS  - what is happening with that?  With MLIR?

   Reimplement a bunch of things that will need to throw away.  When
   added sparse output, that unblocked it.  Assuming regular math
   rules – have an internal design that they are translating and
   upstreaming into MLIR.  Will be possible to do this.  Sparse
   compiler making with a simi ring -
   https://dl.acm.org/doi/abs/10.1145/3485505

   Can make graph sparse possible – can specify which element can be
   an identity – won’t take

Q: Which plugins – should they be written in python only or C++?

   Need a thin layer of Python object or wrapper to hand around – then
   python function wrapper.  Whatever is happening lower (layers) can
   be – C or C++ - just need enough python code to manipulate from the
   python interpreter

Q: Part of an internal structure of a “type” – capability but hasn’t
   pushed on the type system.

   Type system must be granular enough so they know what the backend
   can handle for any layout.

Q: Is that an oneAPI backend for all devices?  Graph BLAS on other
   architectures?

   No catchall solution for graphics (for all devices).  Have a
   solution for people to plug in backends – but people have to
   implement

2021-11-10
==========

Agenda
------

================================================================  ===============================  =============
Overview of oneAPI and SYCL: how all the pieces fit together      Andrew Richards, Codeplay        5 min
Mapping AI software to SYCL and oneAPI: ONNX, Eigen, TensorFlow   Mehdi Goli, Codeplay             20 min
Mapping SYCL to accelerator hardware, using RISC-V as an example  Alastair Murray, Codeplay        20 min
Experience of using SYCL and oneAPI with National Labs            Gordon Brown, Codeplay           15 min
Opens / Topics                                                    All                              30 min
================================================================  ===============================  =============

Attendees
---------

=================================   ===============================
Richards, Alison, Intel             Guoliang, Vastaitech (来)
Alastair Murray, Codeplay           Ashbaugh, Ben, Intel
Hanchinmani, Milind, Intel          Dolbeau, Romain, SiPearl
Andrew Chen (来宾), vastaitech      Ike, Atsushi/池 敦, Fujitsu
Li, Wei, Intel                      Mehdi Goli, Codeplay
Brodman, James, Intel               ICT Lixian Ma (来宾), ICT CAS
Li, Jian Hui, Intel                 Ruyman Reyes, Codeply
Rayanki, Sreenivasulu, Intel        Brown, Gordon, Codeplay
Tamir, Guy, Intel                   Andrew Richards, Codeplay
Petrov, Nikolay A, Intel            Penporn Koanantakool, Google
Nalawadi, Rajeev K, Intel           Curley, Joseph C, Intel
En Shao (来宾), ICT CAS             Sheng Zha, AWS, Apache MX Net
=================================   ===============================

Slides_

.. _Slides: presentations/Codeplay-oneAPI-AI-TAB-Nov2021.pdf

Questions and Answer
--------------------

* Is Codeplay upstreaming the ONNX stuff?  It is all open source and
  it will be up-streamed very soon.
* Does the SYCL EP claim the entire graph or is it claiming in the
  form of multiple subgraphs?  It actually supports the entire graph
  and distributes it to the entire backend.  SYCL execution goes on
  SYCL DNN and all of the implementation of those nodes are available.
* Will the SYCL backend replace all the other backends inside ONNX RT?
  If Codeplay were the owner of it, it should replace all other
  backends…

  No, each existing backend has its own pros and cons. Vendor
  optimized backend are useful when speed/performance on a particular
  device is the key. Pure SYCL-backend would be useful when
  portability on various devices is the key. Especially for devices
  that are new or do not have rich library ecosystem, by enabling SYCL
  they can benefit from the SYCL library ecosystem, supported by
  multiple vendors. You can also have cross-platform performance
  portability via SYCL interoperability with other backends. This
  approach integrates the existing vendor optimised backend in SYCL to
  provide a unique SYCL-interface for memory management and runtime
  control from the user’s point of view while reusing the highly
  optimised vendor backend. oneAPI initiative approach has already
  enabled cross-platform performance portability support on oneDNN via
  SYCL-interoperability for both CUDA and OpenCL-based backend.
* Do you always beat oneDNN or are you as good as oneDNN?  You get
  both portability and performance.
* Can you get both portability and performance?  There is always a
  trade-off between performance and level of abstraction that leads to
  portability.  An assembly code can beat application written in
  high-level language in terms of performance, but will struggle in
  terms of portability.
* The question is how close you are?  Are you achieving 80% of
  customized library?  75% of performance we can achieve – range
  between 75%-100%.  It is important to clarify that if you have an
  interface like oneDNN, you have a common API for all the vendors as
  well as SYCL.  It is difficult to replace HW vendor libraries when
  not all HW vendors provide enough performance counters and detailed
  hardware information
* Do you support training?  At the moment no; inference mode at the
  moment.  We do support training through the TF backend and oneDNN
  backend.
* How to build the DPC tool chain with support for HIP and AMD.  Is
  there some open source for the DPC and SYCL support.  YES – all of
  this is upstreamed into Intel LLVM depository…  The tool chain has
  been open sourced in there – YES.
* On the collectives, what is the scale out support?  This refers to
  the SYCL support with the CUDA backend targeting NVIDIA HW Support.
  There hasn’t been anything done for oneCCL or any other API.
* Do we have some implementations for the group collectives?  Memory
  copy to do some support for the communication node between different
  nodes?  These collectives are for within the kernel functions so
  they are only for a single kernel, they don’t extend into multiple
  nodes.  There is no communication between different nodes – only
  within oneGPU.  Group collectives following the SYCL naming.

2021-08-10
==========

Agenda
------

=======================================  =====================  =============
Why oneAPI, DPC++ Kick-off               James Brodman, Intel   30 min
oneAPI Threading Building Blocks         Mike Voss, Intel       25 min
oneAPI Data Analytics Library (oneDAL)   Nikolay Petrov, Intel  25 min
Opens / Topics                           All                    10 min
=======================================  =====================  =============

Attendees
---------

=================================   ===============================
Andrew Richards, Codeplay           Andrey Nikolaev, Intel
Mehdi Goli, Codeplay                Sujoy Saraswati, Habana
Atsushi Ike, Fujitsu                Tong Gu, Intel
Kentaro Kawakami, Fujitsu           Meena Arunachalam, Intel
Penporn Koanatakool, Google         Alison Richards, Intel
Sheng Zha, Apache MxNet             James Brodman, Intel
Judy Fox, University of Virginia    Michael Voss, Intel
Jun Qian, Vast AI Tech              Ligang Tian, Intel
Andrew Chen, Vast AI Tech           Guy Tamir, Intel
Nikolay A Petrov, Intel             Jian Hui Li, Intel
Rahul Khanna, Intel
=================================   ===============================

Slides
------

`DPC++ <presentations/oneAPI%20and%20Data%20Parallel%20C%2B%2B%20for%20AI%20TAB.pdf>`__

`oneDAL <presentations/AI_TAB_oneDAL%20ML.pdf>`__

`oneTBB <presentations/AI_TAB_oneTBB_0821.pdf>`__

Discussion
----------

Question: Is TBB a good fit for heterogeneous compute or only for CPU?

Answer: Our strategy has been to keep TBB on the host but to work well
alongside of offloading to an accelerator.  Thought about how to
integrate executors into TBB.  We may have our generic algos accept
executors.  There are ways we might expand TBB to accept executors
that offload to accelerators.  In general, though, TBB is the way we
do efficient threading on the host.

|

Question: Are there things SYCL could learn from TBB?

Answer: Could be additional hints given for optimizing performance
with subgroups that could happen.  TBB does have these controls that
could be tuned for performance (or you can use the default and you may
be fine with that).  Setting partitioners.  None of that is exposed
yet in SYCL.

There is not a good interface for expressing graphs yet in SYCL so
there may be a way to gain some learnings there.  In SYCL you have
implicit graphs…but not explicit graphs so this could be an area of
learning from TBB.  Benefit would be to do some optimization and reuse
offload of kernels.  Host offload - give it the chunk it once and then
que up the kernels in a more optimal fashion.  Need to have repeatable
graphs in SYCL.

|

Question: Is this result on CPU or GPU– Slide Scikit Learn for
training and inference

Answer: CPU

Comment: Judy Fox mentioned she is teaching a python course and this
will give a lot of exposrue to Python.  Scikit learn bench – you can
easily download this and try out different sizes and algos and play
with that; Check out Medium.com blog for data analytic software for
additional information.

OPENS
-----

Discuss upcoming topics from the team – happy to have members present
or share topics.  A few ideas:

Andrew Richards, Codeplay: doing simple code and showing how it goes
through SYCL and oneAPI Stack.  They are showing how it ends up on the
HW.  Showing the flow.

Medhi Goli, Codeplay: SYCL integrated w/ Tensorflow – Eigen support
the SYCL standard / oneAPI and how it supports CUDA as well

Codeplay: Support oneAPI on Nvidia GPUs

`Intel Extension for scikit-learn on youTube <https://www.youtube.com/watch?v=h5GamIZDrhE&list=PLg-UKERBljNxsCltpcXU_Haz9xQSCN_SB&index=8>`__


2021-05-20
==========
Attendees:

===============================  ===============================
Alison Richards, Intel           Mourad Gouicem, Intel
Sanjiv Shah, Intel               Daniel M Lavery, Intel
Dmitry Durnov, Intel             Shlomo Raikin, Intel Habana
JF Massol, SiPearl               Rodolfo G Esteves, Intel
Atsushi Ike, Fujitsu             Mehdi Goli, Codeplay
Rajeev K Nalawadi, Intel         Rahul Khanna, Intel
Wei Cui, Microsoft               Andrew Richards, Codeplay
Jian Hui Li, Intel               Sreenivasulu Rayanki, Intel
Kentaro Kawakami, Fujitsu        Krishna Bhuyan, Intel
Tim Harris, Microsoft            Romain Dolbeau, SiPearl
Ruyman Reyes, Codeplay           Guoliang, VastAI Tech
Guy Tamir, Intel                 Jayaram Bobba, Intel Habana
Igor Lopatin, Intel              Andrew Chen, VastAI Tech
Penporn Koanantakool, Google     AG Ramesh, Intel
Emad Barsoum, Cerebras           Andrey Nikolaev, Intel
Zack S Waters, Intel             Guangming Tan, ICT CAS
En Shao, ICT CAS
===============================  ===============================

Welcome, Vision, oneAPI spec update - Sanjiv Shah, Intel

* `Slides <presentations/2021-05-20-oneapi-spec.pdf>`__
* Question: How does oneAPI differ from ROCM?

  oneAPI is across multiple HW platforms and CPU, GPU, FPGA and
  Accelerators, where ROCM is for AMD HW.  One could port Level Zero
  to run on ROCm

Antares for SYCL - Wei Cui, Microsoft

* `Slides <presentations/Antares4SyCL.pdf>`__

TensorFlow and oneDNN in Partnership - Penporn Koanantakool, Google

* `Slides <presentations/2021-05-20-TF-and-onednn.pdf>`__
* Question: Will XLA and Jit be supported?

  Jit is already used inside oneDNN.  Currently XLA is not using oneDNN.

Intel Extension for TensorFlow Demo - Jian Hui Li, Intel

* Intel extension for TensorFlow (TEX) uses modular TensorFlow
  interface to bring intel XPU to TensorFlow for AI workload
  acceleration.  oneAPI complements modular TensorFlow to provide
  modular software architecture and unifies the programming interface
  for AI hardware.

* Questions:

  * Is the code private or can people access it?

    It is private for now but will be public.  Right now it is
    pre-release trial and early exploration.

  * If the new HW support oneDNN, will the integration to Framework just work?

    ITEX uses oneAPI components includes oneDNN, oneCCL, and DPC++.
    If the HW only supports oneDNN only, then only the most
    performance critical subgraph is offloaded to hardware.
    Supporting DPC++ and oneCCL can offload the whole deep learning
    graph to the physical device.

  * What is the preferred way of adding new devices to TF?

    Pluggable devices is the preferred way to plug in new devices to
    TF.

Enable Deep Learning Frameworks at Scale - Dmitry Durnov, Intel

* `Slides <presentations/ai_tab_oneccl.pdf>`__

* Feedback: this is a theoretical concept; can we take the concepts
  and model the exercises in a real world example with x number of
  training exercises.


2021-02-11
==========
Attendees:

===============================  ===============================
Ben Ashbaugh, Intel              Jeff McVeigh, Intel
Krishna Bhuyan, Intel            Rajeev  Nalawadi, Intel
Jayaram Bobba, Habana            Nikolay Petrov,  Intel
Andrew Chen, Vastai Tech         Jun Qian, Vastai Tech
Robert Cohn, Intel               Shlomo Raikin, Habana
Neel Dhamdhere, Intel            AG Ramesh, Intel
Mehdi Goli, Codeplay             Sreenivasulu Rayanki, Intel
Tim Harris, Microsoft            Leif Reinert, AWS
Atsushi Ike, Fujitsu             Ruyman Reyes, Codeplay
Kentaro Kawakami, Fujitsu        Alison Richards, Intel
Rahul Khanna, Intel              Andrew Richards, Codeplay
Kazui Kimihiko, Fujitsu          Tatiana Shpeisman, Google
Penporn Koanantakool, Google     Shivani Sud, Intel
Guoling Li, Vastai Tech          Guy Tamir, Intel
Hui Li, Intel                    Zack Waters, Intel
Jian Hui Li, Intel               Louis Zhang, Vastai Tech
Wei Li, Intel
===============================  ===============================

| oneAPI Welcome & Introduction – Jeff McVeigh
| AI Machine Learning Accelerators – Wei Li :  `Slides <presentations/AI-TAB-Feb-2021.pdf>`__

oneDNN on ARM – Kentaro Kawakami : `Slides <presentations/oneAPI_development_of_oneDNN_for_Armv8-A_SVE_20210210_v4.pdf>`__

* How can we use Mesh TF widely to larger user base?

  Fujistu team is working on Pull request with Google Mesh TF.

oneDNN Graph API – Jian Hui Li : `Slides <presentations/oneDNNGraph-oneAPIAITAB.final.pdf>`__

* How easy is it to add Graph Optimizations to new HW Backends?

  Backends can develop their own graph optimizations to generate the
  best optimized code. The implementation of oneDNN Graph API contains
  an API layer and targets specific backends.  API layer focuses on
  standardizing the operation and graph structure, which is then pass
  to backends for optimization.  oneDNN Graph tensor supports opaque
  tensors which allow backends to use private layout across the
  partitions. We are aware that there is extra integration complexity
  for framework to adopt opaque layouts, so the opaque tensor design
  considered ease of use.  For backends which target large partition,
  it can use the opaque tensor internally and use the public tensor as
  partition input and output.

* Can one use SYCL for custom operations in a graph?

  oneDNN Graph defines a set of operations.  Intel extensions for
  Frameworks have DPC++/SYCL implementation of framework operations
  outside of oneDNN Graph.  If the device implements oneDNN Graph and
  is DPC++/SYCL compatible, it gets the maximum benefit of reusing
  oneDNN Graph based framework integration and Intel extensions.
  Registering a custom op to oneDNN Graph is in the future plan but
  not defined yet.

* Any integration plans to integrate with MLIR?  Is this orthogonal to
  MLIR or a higher level integration?

  Yes.  MLIR is multi-level IR, and oneDNN Graph op is at the same
  level as high level MLIR dialect. We intercept at high level MLIR
  dialect. We plan to have the integration when TF moves to MLIR as
  the main graph representation.

Level Zero – Ben Ashbaugh : `Slides <presentations/21ww07_AI_TAB_Level_Zero.pdf>`__

* How do you adapt to different processors?  VPU, GPU or larger
  constructs than kernels? Can all processors can be abstracted?

  Some examples of device flexibility are the different device
  property queries:
  https://spec.oneapi.com/level-zero/latest/core/api.html#device

  The specific case described on the call were command lists, which
  are groups of commands that can represent a larger task graph:
  https://spec.oneapi.com/level-zero/latest/core/PROG.html#command-lists

  If we need specific features for some other processor type we can
  either add it in a future version of the spec, or it can be added as
  an extension:
  https://spec.oneapi.com/level-zero/latest/core/EXT.html

* Can we capture the capability of L0 (Create software with ability to
  query)?

  Please see the link above to the different device property queries.

* Do we need a plug-in for OpenVINO?  Do we need to develop Level-0
  API?

  OpenVINO is powered by oneAPI and is part of oneAPI
  ecosystem. Implementing oneAPI including Level-0 certainly help
  integrating to OpenVINO in a modular way.

* Can oneDNN co-exist with Level Zero?  Or can CPU code generator
  co-exist with Level0?

  Yes, oneDNN can co-exist with Level Zero, and oneDNN is one of the
  layers that can be built on top of Level Zero.  A CPU code generator
  would use a different mechanism currently though, because Level Zero
  is not currently implemented for CPU devices.  See note below:

  oneDNN works on top of L0. Though L0 does not support CPU (so there
  is no sycl::device that uses L0 as a backend).  Here is what oneDNN
  does for each type of device/backend.

::

  DPC++ device ----- CPU device -------------------- CPU jitted code is executed through sycl host_task
                \--- GPU device ---- L0 backend  --- binary is wrapped in L0 module, then in sycl program, and run through SYCL RT
                                 \-- OCL backend --- binary is wrapped in OCL kernel, then in sycl program, and run through SYCL RT

Brainstorm Discussion:  Intros, Requirements, Use Cases, Q& A - All
