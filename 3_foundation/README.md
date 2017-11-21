<!--- @file
  3 Foundation

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

# 3 Foundation

UEFI employs several key concepts as cornerstones of understanding for UEFI
Drivers. These concepts are defined in the _UEFI Specification_. Programmers
new to UEFI should find the following introduction to a few of UEFI's key
concepts helpful as they study the _UEFI Specification_.

The basic concepts covered in the following sections include:

* Basic programming model

* Objects managed by UEFI-conformant firmware

* UEFI system table

* Handle database

* Protocols

* UEFI images

* Events

* Task priority levels

* Device paths

* UEFI driver binding model

* Platform initialization

* Boot manager and console management

* EDK II libraries

As each concept is discussed, the related application programming interfaces
(APIs) are identified along with references to the related sections in the
_UEFI Specification_.

One of the components available from the EDK II open source project and
distributed with the UDK2010 releases is the UEFI Shell; a command line
interface with useful commands for development and testing of UEFI drivers and
UEFI applications. The UEFI Shell also provides commands to help illustrate
many of the basic concepts described in the sections that follow. These useful
UEFI Shell commands are identified as each concept is introduced. The UEFI
Shell is an open source project at
[_http://www.tianocore.org_](http://www.tianocore.org/) where documents
providing details on all of the available commands can be found.
