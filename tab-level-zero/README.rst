=========================================================
Level Zero Technical Advisory Board Meeting Meeting Notes
=========================================================

2022-6-9
========

Agenda
------

.. 2 column table for topic and speaker

.. list-table::

   * - Something about level zero
     - Zack Waters, Intel

Attendees
---------

.. 2 column table of attendees

.. list-table::

   * - Zack Water, Intel
     - Alison Richards, Intel
   * - Ben Ashbauch, Intel
     -


Slides
------

`Intro <presentations/oneAPI%20AI%20TAB%20intro%20March%208%202022.pdf>`__

`Interfacing oneAPI and Python`_

_`Interfacing oneAPI and Python`: presentations/Data-Parallel-Essentials-For-Python-oneAPI-TAB.pdf

Interfacing oneAPI and Python
-----------------------------

.. notes for the topic

Q: Why did you not use Buffers?

A: While it is technically possible, different Python classes would
   need to be created for every supported buffer data type as the
   buffer and accessor type definitions require the type of the
   underlying elements. We can get around the issue by using “untyped”
   buffers, but that brings its own challenges as partitioning of
   buffers can lead to loss of precision and incorrect results.
