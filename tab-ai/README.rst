==============================================================
oneAPI Technical Advisory Board Meeting (TAB-AI) Meeting Notes
==============================================================

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

oneDNN on ARM – Kentaro Kawakami

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
