<!--- @file
  README.md for EDK II Driver Writer's Guide for UEFI 2.3.1

  Copyright (c) 2012-2018, Intel Corporation. All rights reserved.<BR>

  Redistribution and use in source (original document form) and 'compiled'
  forms (converted to PDF, epub, HTML and other formats) with or without
  modification, are permitted provided that the following conditions are met:

  1) Redistributions of source code (original document form) must retain the
     above copyright notice, this list of conditions and the following
     disclaimer as the first lines of this file unmodified.

  2) Redistributions in compiled form (transformed to other DTDs, converted to
     PDF, epub, HTML and other formats) must reproduce the above copyright
     notice, this list of conditions and the following disclaimer in the
     documentation and/or other materials provided with the distribution.

  THIS DOCUMENTATION IS PROVIDED BY TIANOCORE PROJECT "AS IS" AND ANY EXPRESS OR
  IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
  MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO
  EVENT SHALL TIANOCORE PROJECT  BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
  SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
  PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS;
  OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
  WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
  OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS DOCUMENTATION, EVEN IF
  ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

-->

<img src="media/TianocoreTitlePageLogo.jpg" width="300" />

### {{ book.title }}

{% if book.draft %}
** DRAFT FOR REVIEW **
{% else %}
** {{ book.version }} **
{% endif %}

** {{ gitbook.time|date('MM/DD/YYYY hh:mm:ss') }} **

{% if book.udkrelease %}
** {{ book.udkrelease }} **
{% endif %}


### Acknowledgements

Redistribution and use in source (original document form) and 'compiled'
forms (converted to PDF, epub, HTML and other formats) with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code (original document form) must retain the
   above copyright notice, this list of conditions and the following
   disclaimer as the first lines of this file unmodified.

2. Redistributions in compiled form (transformed to other DTDs, converted to
   PDF, epub, HTML and other formats) must reproduce the above copyright
   notice, this list of conditions and the following disclaimer in the
   documentation and/or other materials provided with the distribution.

THIS DOCUMENTATION IS PROVIDED BY TIANOCORE PROJECT "AS IS" AND ANY EXPRESS OR
IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO
EVENT SHALL TIANOCORE PROJECT  BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS;
OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS DOCUMENTATION, EVEN IF
ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

Copyright (c) 2012-2018, Intel Corporation. All rights reserved.

<div style="page-break-after: always;"></div>

**Revision History**

| `Revision` | `Decscription`                                                                                 | `Date`    |
| ---------- | ---------------------------------------------------------------------------------------------- | --------- |
| 0.31       | Initial draft.                                                                                 | 4/3/03    |
| 0.70       | Initial draft. Edited for formatting and grammar.                                              | 6/3/03    |
| 0.90       | Incorporated industry review comments.                                                         | 7/20/04   |
|            | Updated the coding conventions.                                                                |           |
|            | Updated for the 1.10.14.62 release of the EFI Sample Implementation.                           |           |
|            | Updated the supported versions of Microsoft Visual Studio* and Windows*.                 |           |
|            | Removed TBD sections that appeared in the 0.7 version. Edited for grammar and formatting.      |           |
| 0.91       | Updated for UEFI 2.0                                                                           | 10/31/06  |
| 0.92       | New formatting                                                                                 | 11/27/06  |
| 0.93       | Review feedback incorporated                                                                   | 1/14/2007 |
| 0.94       | Additional formatting                                                                          | 2/27/2007 |
| 0.95       | Additional formatting                                                                          | 3/23/2007 |
| 0.96       | Additional formatting                                                                          | 4/25/2008 |
| 0.97       | Clarify role of EDK as being implementation-specific and added definitions myriad of library references so the meaning of the implementation specific code examples could be clarified without having to reference documents aside from the UEFI Specification.  | 6/25/2008 |
| 0.98       | Updated for UEFI 2.3.1 and EDK II                                                              | 2/12/12   |
| 1.00       | Review feedback incorporated, additional formatting                                            | 2/27/12   |
| 1.01       | Review feedback incorporated                                                                   | 3/8/12    |
| 1.1        | Conversion to GitBook markdown format                                                          | 4/18/18   |
