.. image:: https://github.com/oneapi-src/oneAPI-tab/actions/workflows/checks.yaml/badge.svg
   :target: https://github.com/oneapi-src/oneAPI-tab/actions

================================
 oneAPI Community Forum
================================

The oneAPI Community Forum exists to define a standards-based,
cross-architecture open specification for accelerated computing and
to foster the open-source implementations of the specification.

More information can be found at oneapi.io_.

This repository hosts notes and presentation materials from the
oneAPI Community Forum meetings.  The meetings are open and comprised
of experts from industry, government, and academia that help guide
the oneAPI specification.

The policies and governance processes are also available on this repo.

The community is invited to join the meetings, review the `oneAPI
Specification`_, and read the information in this repo. Contributions
can be made by joining the Special Interest Groups (SIGs) or by
posting comments or questions as GitHub issues. General questions can
go to this repo and issues specific to parts of the specification can
go to the `Specification repo`_.

To be notified of new meeting notes, become a watcher of this repo. If
you have a question about how to join the SIGs, email
`oneapi@codeplay.com`_.

Read about the `oneAPI Community Forum governance`_ to understand
the organization and processes.

.. _oneapi.io: https://oneapi.io
.. _`oneAPI Specification`: https://spec.oneapi.io
.. _`Specification repo`: https://github.com/oneapi-src/oneapi-spec
.. _`oneapi@codeplay.com`: mailto:oneapi@codeplay.com
.. _`oneAPI Community Forum governance`: organization

oneAPI Community Forum Special Interest Groups (SIGs)
-----------------------------------------------------

SIGs host regular meetings to organize community proposals and
contributions to the oneAPI specification. They also act as a bridge
between the community and developers working on implementations of
the oneAPI specification.

* `Language <language>`__ - This group covers topics related to
  language implementations that integrate with the oneAPI
  specification.

* `Math <math>`__ - This groups covers topics related to math
  operations.

* `AI <ai>`__ - This group covers topics related to AI operations.

* `Hardware <hardware>`__ - This group covers topics related to the
  integration of hardware and how this is defined in the oneAPI
  specification.

Upcoming oneAPI Community Forum Meetings
----------------------------------------

.. list-table::
   :header-rows: 1

   * - Date
     - Meeting Type
     - Location
     - How to join
   * - November 3rd 2022, 9am US Central Time
     - Hardware SIG
     - Virtual
     - Contact_
   * - November 14th 2022, 5:30pm US Central Time
     - SC22 Meetup
     - Fairmont Dallas
     - `RSVP Form`_
   * - December 14th 2022, 9am US Central Time
     - Annual Meeting
     - Virtual
     - Contact_

.. _Contact: https://www.oneapi.io/community
.. _`RSVP Form`: https://forms.office.com/r/sQdM4unYSP

Find the minutes for prior meetings in the appropriate SIG section of
this repository.

Publishing Meeting Notes from Google Docs
-----------------------------------------

To ensure access for all, we mirror meeting notes from Google Docs in
GitHub as a PDF. Here is the procedure.

Edit the meeting notes in Google Docs. If you want to publish a
presentation, upload a PDF to GitHub. For example, here is the
directory that contains presentations for the cross-tab:
presentations_. Select *Add file*, then *Upload files*. After the file
is uploaded, click on its name to view and then copy the URL from your
browser to the google doc.

When meeting notes are complete, publish them to GitHub as a
PDF. Mirroring is triggered automatically once a day. If you do not
want to wait, go to `mirror workflow`_, and select *Run workflow* and
then *Run workflow*. When you see the green check, it is
published. Look at `mirroring yaml`_ to get the URL. For example, see
`Cross-tab PDF`_.

.. _presentations: https://github.com/oneapi-src/oneAPI-tab/tree/main/cross-tab/presentations
.. _`mirror workflow`: https://github.com/oneapi-src/oneAPI-tab/actions/workflows/mirror-google-docs.yaml
.. _`mirroring yaml`: .github/workflows/mirror-google-docs.yaml
.. _`Cross-tab PDF`: https://oneapi-src.github.io/oneAPI-tab/meeting-notes/cross-tab.pdf
