<!--- @file
  18.7.2 Using INF File to Generate PCI Option ROM Image

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

### 18.7.2 Using INF File to Generate PCI Option ROM Image

Use the INF file to specify the information required to package a UEFI Driver
into a PCI Option ROM image without having to manually run the `EfiRom` utility. _Chapter
7_ covers Driver Entry Points and includes a number of example INF files. The _following
example_ shows an expanded version of the `AbcDriverMinimum` from _Chapter 7_ and also
shows how the PCI Option ROM related information can be specified. The only
changes are the addition of the PCI statements in the [Defines] section. These
PCI statements allow the Vendor ID, Device ID, Class Code, and Revision values
to be specified and they are used to fill in the PCI Option ROM headers. The
`PCI_COMPRESS` statement specifies whether the UEFI Driver should be compressed
using the UEFI compression algorithm or not. If a statement is not present, the
value is assumed to be 0 If the PCI statements are present, and if the UEFI
Driver is successfully built, the PCI Option ROM image is then automatically
generated. The one limitation of this method is that the PCI Option ROMs are
allowed to contain only a single UEFI Driver.

###### Example 199-UEFI Driver INF File for PCI Option ROM

```ini
[Defines]
  INF_VERSION    = 0x00010005
  BASE_NAME      = AbcDriverPciOptionRom
  FILE_GUID      = DA87D340-15C0-4824-9BF3-D52286674BEF
  MODULE_TYPE    = CAE55A8A-4307-4ae1-824E-326EE24928D7
  VERSION_STRING = 1.0
  ENTRY_POINT    = AbcDriverEntryPoint
  PCI_VENDOR_ID  = 0xABCD
  PCI_DEVICE_ID  = 0x1234
  PCI_CLASS_CODE = 0x56789A
  PCI_REVISION   = 0x0003
  PCI_COMPRESS   = TRUE

[Sources]
  Abc.c

[Packages]
  MdePkg/MdePkg.dec

[LibraryClasses]
  UefiDriverEntryPoint
```
