==============================================================
oneAPI Technical Advisory Board Meeting (TAB-AI) Meeting Notes
==============================================================

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

* Any integration plans to integrate with MLIR?  Is this orthogonal to MLIR or a higher level integration?

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
