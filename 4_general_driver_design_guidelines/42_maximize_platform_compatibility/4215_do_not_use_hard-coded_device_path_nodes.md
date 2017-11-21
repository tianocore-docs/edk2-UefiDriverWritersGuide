<!--- @file
  4.2.15 Do not use hard-coded device path nodes

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

### 4.2.15 Do not use hard-coded device path nodes

The `ACPI()` node in the EFI Device Path Protocol identifies the PCI root
bridge in the ACPI namespace. The _ACPI Specification_ allows _HID to describe
vendor-specific capability and _CID to describe compatibility. Therefore, there
is no requirement for all platforms to use the PNP0A03 identifier in the _HID
to identify the PCI root bridge. The following are the only requirements for
the PCI root bridge:

* The PNP0A03 identifier must appear in _HID if a vendor-specific capability
  description isn't needed.

* The PNP0A03 identifier must appear in _CID if _HID contains a vendor-specific
  identifier.

To avoid problems with platform differences, UEFI drivers should not create
UEFI device paths from hard-coded information. Instead, UEFI bus drivers should
append new device path nodes to the device path from the parent device handle.

#### 4.2.15.1 PNPID byte order for UEFI

The ACPI PNPID format (byte order) follows the original EISA ID format. UEFI
also uses PNPID in the device path ACPI nodes. However, for a given string,
ACPI and UEFI do not generate the same numbers. For example:

```
HID = "PNP0501"
ACPI = 0x0105D041
EFI = 0x050141D0
```

The significance is that operating systems that try to match the UEFI ACPI
device path node to the ACPI name space must perform a translation.

Refer to _Chapter 4_ of this guide for information about lengths of words on
32-bit versus 64-bit architectures.

#### 4.2.15.2 Working with UEFI Device Path Nodes

UEFI Device Paths Nodes are not required to be aligned. If the proper coding
style is used when working with device paths, a UEFI Driver can be implemented
to guarantee all that fields of UEFI Device Path Nodes are accessed with
natural alignment. This improves platform compatibility, especially for IPF
platforms.

**********
**TIP:** Do not assume that, when given a device path, that the path is
aligned. Copy pieces of the device path to a known-aligned device path before
accessing it. The device path may then be accessed safely. Alternatively, use
EDK II **BaseLib** functions to perform unaligned reads and writes.
**********
