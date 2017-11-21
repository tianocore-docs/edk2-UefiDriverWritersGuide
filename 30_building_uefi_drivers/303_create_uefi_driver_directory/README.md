<!--- @file
  30.3 Create UEFI Driver Directory

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

## 30.3 Create UEFI Driver Directory

The next step is to create a subdirectory in an EDK II package for the UEFI
Driver contents including an INF file and all source files required to build
the UEFI Driver. There are no restrictions on the directory structure
organization within an EDK II package. The examples shown here are simple and
only use one layer of directories. The `MdeModulePkg` is an example of an EDK
II package with about 100 UEFI Drivers and a more complex directory structure
to organize the UEFI Drivers based on the protocol they consume and the
features they provide.

_Appendix A_ contains a template for an INF file for a UEFI Driver and a UEFI
Runtime Driver. This template should be sufficient for most UEFI Driver
implementations. The EDK Build Specifications on
[_http://www.tianocore.org_](http://www.tianocore.org/) provide the full
description of INF files and their supported syntax for describing all the
packages, sources, library classes, protocols, and GUIDs required to compile
and link a UEFI Driver.

The example below shows an example directory structure for an EDK II WORKSPACE
after creating the new package called `MyDriverPkg` following the steps listed
above and creating a subdirectory called `MyDriver` and adding an INF file and
a C source file.

###### Example 258-UEFI Driver Directory

```
BaseTools/ Conf/
MdePkg/
MdeModulePkg/
OptionRomPkg/ MyDriverPkg/
MyDriverPkg.dec
MyDriverPkf.dsc Include/
Protocol/ Guid/
Library/ MyDriver/
MyDriver.inf
MyDriver.c
```

The following example shows an example INF file
`MyDriverPkg/MyDriver/MyDriver.inf`. Every new INF must have a unique
`FILE_GUID` value and `BASE_NAME`. This INF file example only uses the services
from a single library class called `UefiDriverEntryPoint`. Every UEFI Driver
must use this Library Class. Examples in earlier chapters show more complex
driver examples that use more library classes. The DSC file in the previous
section contains a mapping for the `UefiDriverEntryPoint` library and that
mapping is to `MdePkg/Library/UefiDriverEntryPoint/UefiDriverEntryPoint.inf`.

###### Example 259-UEFI Driver INF File

```ini
[Defines]
  INF_VERSION    = 0x00010005
  BASE_NAME      = MyDriver
  FILE_GUID      = 1C0D95A7-C0D6-4054-9245-8E2C81FC9ECD
  MODULE_TYPE    = UEFI_DRIVER
  VERSION_STRING = 1.0
  ENTRY_POINT    = MyDriverEntryPoint

[Sources]
  MyDriver.c

[Packages]
  MdePkg/MdePkg.dec

[LibraryClasses]
  UefiDriverEntryPoint
```

The following example shows a sample C source file MyDriverPkg/MyDriver/MyDriver.c that does not do anything other than
  just return EFI_SUCCESS.
  
###### Example 260-UEFI Driver C Source File

```c
#include <Uefi.h>

EFI_STATUS
EFIAPI
MyDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  return EFI_SUCCESS;
}
```
