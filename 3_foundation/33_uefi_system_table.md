<!--- @file
  3.3 UEFI system table

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

## 3.3 UEFI system table

The UEFI system table is the most important data structure in UEFI. From this
one data structure, a UEFI executable image can gain access to system
configuration information and a rich collection of UEFI services. These UEFI
services include the following:

* UEFI boot services

* UEFI runtime services

* Services provided by protocols

Two of the data fields in the UEFI system table, UEFI boot services and UEFI
runtime services, are accessed through the UEFI boot services table and the
UEFI runtime services table, respectively. The number and type of services that
are available from these two tables are fixed for each revision of the _UEFI
Specification_. The UEFI boot services and UEFI runtime services are defined in
the _UEFI Specification_. The specification also describes the common uses of
these services by UEFI drivers.

Protocol services are groups of related functions and data fields that are
named by a Globally Unique Identifier (GUID; see Appendix A of the _UEFI
Specification_). Protocol services are typically used to provide software
abstractions for devices such as consoles, mass storage devices and networks.
They can also be used to extend the number of generic services that are
available in the platform.

Protocols are the basic building blocks that allow the functionality of UEFI
firmware to be extended over time. The _UEFI Specification_ defines over 30
different protocols, and various implementations of UEFI firmware. UEFI drivers
may produce additional protocols to extend the functionality of a platform.
