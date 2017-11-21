<!--- @file
  18.7 PCI Option ROM Images

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

## 18.7 PCI Option ROM Images

The EDK II provides tools to aide in the development of UEFI drivers for PCI
adapters. Once UEFI Driver(s) for a PCI adapter are built, they need to be
packaged into PCI option ROM compatible image format. UEFI drivers stored in
PCI option ROMs are automatically loaded and executed by the PCI bus driver
during PCI enumeration.

The EDK II tools provide two methods to generate a PCI Option ROM image. These
are the `EfiRom` utility and the EDK II INF/FDF file syntax.

Using the `build` command, each allows a UEFI Driver developer to describe how
UEFI Drivers should be packaged into a PCI Option ROM image as part of the standard
EDK II build process.

Use either PCI Option ROM image with a PROM programmer or a flash update
utility to reprogram the PCI option ROM container on a PCI adapter.
