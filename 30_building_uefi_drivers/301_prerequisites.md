<!--- @file
  30.1 Prerequisites

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

## 30.1 Prerequisites

Before a UEFI Driver can be built, an EDK II build environment must be
established on a development system. The EDK II project is maintained by the [TianoCore community](http://www.tianocore.org/) on [GitHub](https://github.com/tianocore/tianocore.github.io/wiki/EDK-II), and validated releases of the EDK II project are periodically posted. The current validated release as of June, 2017 is [UDK2017](https://github.com/tianocore/edk2/releases/tag/vUDK2017). It is **recommended** that a validated release be used for UEFI Driver development instead of the trunk of the EDK II project, because the trunk is under active development. The validated release
page includes links to documentation to help setup a build environment on a
development system. Verify that one of the standard platforms builds correctly
before proceeding.

In most cases, building a UEFI Driver only requires a few directories from an
EDK II build environment. These include:

* `BaseTools` - Contains EDK II build tools

* `Conf` - Contains configuration files for EDK II build tools and supported
  compilers and linkers

* `MdePkg` - Contains the include files and libraries to support industry
  standard specifications. This content includes all of the published _UEFI
  Specifications_ and _EFI Specifications_ as well as includes files for
  industry standard buses such as PCI, USB, and SCSI.

* `MdeModulePkg` - Contains UEFI Drivers that can be used as reference. Also
  contains HII related libraries that may be used by UEFI Drivers that produce
  HII packages.

* `OptionRomPkg` - Sample package with three UEFI Drivers and a UEFI
  Application that can be used as a template for a device manufactures own
  package for UEFI Driver development.

* `MyDriverPkg` - Example EDK II package that contains the UEFI Drivers
  implemented by a device manufacturer. This directory name is only used for
  discussion purposes. Device manufacturers may generate their own directory
  name for their own package and may generate more than one package for their
  UEFI Driver content if required.
