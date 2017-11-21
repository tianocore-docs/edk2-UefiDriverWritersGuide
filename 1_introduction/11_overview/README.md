<!--- @file
  1.1 Overview

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

## 1.1 Overview

This document is designed to aid in the development of UEFI Drivers using the
EDK II open source project as a development environment. The EDK II provides a
crossplatform firmware development environment for UEFI. UEFI Drivers are
described in the _Unified Extensible Firmware Interface Specification (hereafter referred to as the _"UEFI Specification"_). There are
different categories of UEFI Drivers, and many variations of each category.
This document provides basic information for the most common categories of UEFI
drivers. Many other driver designs are possible.

In addition, this document covers the design guidelines and recommendations for
the different driver-related UEFI Protocols, along with the design guidelines
for PCI, USB, SCSI, ATA, Consoles, Serial Ports, Graphics, Mass Storage,
Network Interfaces and User Credentials.

Finally, this document discusses UEFI Driver porting considerations and UEFI
Driver optimization techniques for Intel IA-32-, Intel x64- and Intel(R)
Itanium(R)-based platforms, as well as EFI Byte Code (EBC) platform types
supported by the _UEFI Specification._

The UEFI Driver Writers Guide uses the names defined by the EDK II open source
project when referring to the various platform types.

* `IA32`-Intel IA-32 platforms

* `X64`-Intel(R) 64 platforms

* `IPF`-Intel(R) Itanium(R)-based platforms

* `EBC`-EFI Byte Code platforms

### 1.1.1 Assumptions

This document assumes that the reader is familiar with the following:

* Unified Extensible Firmware Interface Specification, Version 2.3.1.

* The EDK II is an open-source build environment project that is under constant
  development. EDK II not only provides the build environment, but also
  provides build tools and source code for firmware and drivers.

**********
**Note:** _The [EDK II project](https://github.com/tianocore/tianocore.github.io/wiki/EDK-II) of
[TianoCore](http://www.tianocore.org/) is under active
development, often on a daily basis. Be sure to use a validated release of
[UDK2010](https://github.com/tianocore/tianocore.github.io/wiki/UDK2010) for all UEFI Driver development._
**********

* The _UDK2010 Developer's Kit_, referred to in this guide as the _UDK2010,_
  contains EDK II validated common-core sample code. The open-source UDK2010 is
  a stable build of the EDKII project and has been validated on a variety of
  Intel platforms, operating systems and application software. The open-source
  UDK2010 is available for download at www.tianocore.org

* The UDK2010 supports UEFI Driver development using the following operating
  system environments: Microsoft Windows*, UNIX and like systems and MAC OS
  X(R). Refer to [_http://www.tianocore.org_](http://www.tianocore.org/) for a
  complete list of current development operating systems.

* The UDK2010 supports the development of UEFI Drivers using several families
  of compilers including those from Microsoft*, Intel and GCC. Refer to
  [_http://www.tianocore.org_](http://www.tianocore.org/) for a complete list
  of currently supported compilers.
