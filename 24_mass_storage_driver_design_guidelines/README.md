<!--- @file
  24 Mass Storage Driver Design Guidelines

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

# 24 Mass Storage Driver Design Guidelines

This chapter covers the general guidelines for implementing UEFI Drivers for
mass storage devices. Most mass storage devices reside on industry standard
busses such as ATA, SCSI, or USB. This means that the design guidelines as
described in _Chapter 21_ for ATA, _Chapter 20_ for SCSI, or _Chapter 19_ for
USB must be followed along with the general guidelines described in _Chapter 4_
of this guide.

If a mass storage device is intended to be used as a boot device for a UEFI
operating system or UEFI applications, then a UEFI Driver must be implemented
that produces the Block I/O Protocol. If the UEFI Driver is required to be
conformant with the _UEFI Specification 2.3.1_ or higher, then the Block I/O 2
Protocol must also be produced. If the mass storage device supports the SPC-4
or ATA8-ACS security commands, then the Storage Security Command Protocol must
also be produced. A mass storage device must either directly support or be able
to emulate the following operations:

* Read blocks of data from the mass storage device.

* Write blocks of data to the mass storage device.

* Determine the size of the blocks on the mass storage device.

* Determine the total number of blocks on the mass storage device.

* If the mass storage device supports removable media, then methods must exist
  to determine if media is present, media is not present, and if the media has
  been changed.

If a mass storage device does not meet these requirements, but still must
support being used as a boot device, then consider implementing a UEFI Driver
that produces either the Simple File System Protocol or the Load File Protocol.
Please see the Media Access chapter of the _UEFI Specification_ for details on
the Simple File System Protocol and _Chapter 27_ for details on the Load File
Protocol.

The EDK II provides a set of platform agnostic drivers in the `MdeModulePkg`
and the `FatBinPkg` that consume the Block I/O Protocols and produce the Simple
File System Protocol which is one of the two protocols from which a UEFI Boot Manager is
able to boot a UEFI operating system or a UEFI application. These platform
agnostic drivers allow the contents of the mass storage media to be accessed
without any specialized knowledge of the specific device or controller. The set
platform agnostic drivers UEFI Drivers include:

* `MdeModulePkg/Universal/Disk/DiskIoDxe`

* `MdeModulePkg/Universal/Disk/PartitionDxe`

* `MdeModulePkg/Universal/Disk/UnicodeCollation`

* `FatBinPkg/EnhancedFatDxe`
