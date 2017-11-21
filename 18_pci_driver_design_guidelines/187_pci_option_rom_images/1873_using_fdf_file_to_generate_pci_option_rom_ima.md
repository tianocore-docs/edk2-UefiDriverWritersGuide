<!--- @file
  18.7.3 Using FDF File to Generate PCI Option ROM Image

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

### 18.7.3 Using FDF File to Generate PCI Option ROM Image

When managing large numbers of UEFI Drivers and PCI Option ROMs, greater
flexibility than the `EfiRom` utility or the INF methods allow may be required.
The EDK II build system supports an FDF file format that provides methods to
package UEFI Drivers into FLASH devices. The FDF file format also supports the
description of PCI Option ROMs. The EDK II build system requires a DSC file to
build UEFI Drivers. The DSC file format is covered in more detail in _Chapter
30_. A DSC file can optionally specify an associated FDF file in the
`[Defines]` section of the DSC file with a `FLASH_DEFINITION` statement. The
example below shows the `[Defines]` section of a DSC file specifying the FDF
file `AbcDriver.fdf`. The FDF file is typically in the same directory as the
DSC file.

###### Example 200-Specify name of FDF file from a DSC file

```ini
[Defines]
  PLATFORM_NAME           = AbcDriver
  PLATFORM_GUID           = 14893C02-5693-47ab-AEF5-61DFA089508A
  PLATFORM_VERSION        = 0.10
  DSC_SPECIFICATION       = 0x00010005
  OUTPUT_DIRECTORY        = Build/AbcDriver
  SUPPORTED_ARCHITECTURES = IA32|IPF|X64|EBC|ARM
  BUILD_TARGETS           = DEBUG|RELEASE
  SKUID_IDENTIFIER        = DEFAULT
  FLASH_DEFINITION        = AbcDriver/AbcDriver.fdf
```

The FDF file may describe one or more PCI Option ROM images These PCI Unlike
the INF method, Option ROM images are not limited to a single UEFI Driver. The
following example shows an FDF file that produces three PCI Option ROM images
called `AbcDriverAll.rom`, `AbcDriverIA32.rom`, and `AbcDriverX64.rom`. The
first PCI Option ROM image contains a UEFI Driver image compiled for IA32 and a
UEFI Driver image compiled for X64 The syntax for specifying the PCI related
definitions is the same as the INF example in the previous section. The second
PCI Option ROM image contains only one UEFI Driver compiled for IA32 The third
image contains one UEFI Driver compiled for X64 The UEFI Drivers are compressed
in all three of these option ROM images.

###### Example 201-Using an FDF file to Generate PCI Option ROM images

```ini
[Rule.Common.UEFI_DRIVER]
  FILE
  DRIVER = $(NAMED_GUID) {
  PE32 PE32 |.efi
  }

[OptionRom.AbcDriverAll]
  INF
  USE            = IA32 AbcDriver/Abc.inf {
  PCI_VENDOR_ID  = 0xABCD
  PCI_DEVICE_ID  = 0x1234
  PCI_CLASS_CODE = 0x56789A
  PCI_REVISION   = 0x0003
  PCI_COMPRESS   = TRUE
  }
  INF
  USE            = X64 AbcDriver/Abc.inf {
  PCI_VENDOR_ID  = 0xABCD
  PCI_DEVICE_ID  = 0x1234
  PCI_CLASS_CODE = 0x56789A
  PCI_REVISION   = 0x0003
  PCI_COMPRESS   = TRUE
  }

[OptionRom.AbcDriverIAa32]
  INF
  USE            = IA32 AbcDriver/Abc.inf {
  PCI_VENDOR_ID  = 0xABCD
  PCI_DEVICE_ID  = 0x1234
  PCI_CLASS_CODE = 0x56789A
  PCI_REVISION   = 0x0003
  PCI_COMPRESS   = TRUE
  }

[OptionRom.AbcDriverX64]
  INF
  USE            = X64 AbcDriver/Abc.inf {
  PCI_VENDOR_ID  = 0xABCD
  PCI_DEVICE_ID  = 0x1234
  PCI_CLASS_CODE = 0x56789A
  PCI_REVISION   = 0x0003
  PCI_COMPRESS   = TRUE
  }
```
