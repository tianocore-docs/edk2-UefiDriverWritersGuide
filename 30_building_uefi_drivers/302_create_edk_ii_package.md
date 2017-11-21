<!--- @file
  30.2 Create EDK II Package

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

## 30.2 Create EDK II Package

The first step is to make sure there is an EDK II package available to which a
new UEFI Driver can be added. If an EDK II package has already been created for
UEFI Driver work, then this step may be skipped. Otherwise the following steps
are required:

* Create a new directory that is a peer to `MdePkg` (e.g. `MyDriverPkg`).

* Create a subdirectory called `Include` (e.g. `MyDriverPkg/Include`).

* Create a subdirectory of `Include` called `Protocol` (e.g.
  `MyDriverPkg/Include/Protocol`).

* Create a subdirectory of `Include` called `Guid` (e.g.
  `MyDriverPkg/Include/Guid`).

* Create a subdirectory of `Include` called `Library` (e.g.
  `MyDriverPkg/Include/Library`).

* Add DEC file to the new package directory (e.g.
  `MyDriverPkg/MyDriverPkg.dec`).

* Add DSC file to the new package directory (e.g.
  `MyDriverPkg/MyDriverPkg.dsc`).

The following example shows an example directory structure for an EDK II
WORKSPACE after creating the new package called `MyDriverPkg` following the
steps listed above. The `Include` subdirectory is a place holder in case new
Protocols, GUIDs, or Library Classes are required to support new UEFI Driver
implementations.

###### Example 255-EDK II Package Directory

```
BaseTools/ Conf/
MdePkg/
MdeModulePkg/
OptionRomPkg/ MyDriverPkg/
MyDriverPkg.dec
MyDriverPkf.dsc Include/
Protocol/ Guid/
Library/
```

The following example shows an example DEC file
`MyDriverPkg/MyDriverPkg.dec`. Every new DEC file must have a unique GUID value
and name.

###### Example 256-EDK II Package DEC File

```ini
[Defines]
  DEC_SPECIFICATION = 0x00010005
  PACKAGE_NAME      = MyDriverPkg
  PACKAGE_GUID      = E972EFA5-75CC-4ade-A719-60DD9AE5217B
  PACKAGE_VERSION   = 0.10

[Includes] Include
```

The example below shows an example DSC file `MyDriverPkg/MyDriverPkg.dsc`.
Every new DSC must have a unique `PLATFORM_GUID` value, `PLATFORM_NAME` and
`OUTPUT_DIRCTORY` path. This DSC file example also contains the library mapping
required to build a UEFI conformant UEFI Driver. Many other library mappings
are possible with the content from the EDK II project, but many of this
mappings use services that are not defined by the _UEFI Specification_, so the
use of alternate mapping may produce a UEFI Driver that runs correctly on some
platforms but not others.

###### Example 257-EDK II Package DSC File

```ini
[Defines]
  PLATFORM_NAME           = MyDriverPkg
  PLATFORM_GUID           = 7C297DD4-65D9-4dfe-B609-94330E607888
  PLATFORM_VERSION        = 0.10
  DSC_SPECIFICATION       = 0x00010005
  OUTPUT_DIRECTORY        = Build/MyDriverPkg
  SUPPORTED_ARCHITECTURES = IA32|IPF|X64|EBC|ARM
  BUILD_TARGETS           = DEBUG|RELEASE
  SKUID_IDENTIFIER        = DEFAULT

[LibraryClasses]
  UefiDriverEntryPoint|MdePkg/Library/UefiDriverEntryPoint/UefiDriverEntryPoint.inf
  UefiApplicationEntryPoint|MdePkg/Library/UefiApplicationEntryPoint/UefiApplicationEntryPoint.inf
  UefiBootServicesTableLib|MdePkg/Library/UefiBootServicesTableLib/UefiBootServicesTableLib.inf
  UefiLib|MdePkg/Library/UefiLib/UefiLib.inf
  UefiRuntimeServicesTableLib|MdePkg/Library/UefiRuntimeServicesTableLib/UefiRuntimeServicesTableLib.inf
  UefiRuntimeLib|MdePkg/Library/UefiRuntimeLib/UefiRuntimeLib.inf
  MemoryAllocationLib|MdePkg/Library/UefiMemoryAllocationLib/UefiMemoryAllocationLib.inf
  DevicePathLib|MdePkg/Library/UefiDevicePathLib/UefiDevicePathLib.inf
  UefiUsbLib|MdePkg/Library/UefiUsbLib/UefiUsbLib.inf
  UefiScsiLib|MdePkg/Library/UefiScsiLib/UefiScsiLib.inf
  BaseLib|MdePkg/Library/BaseLib/BaseLib.inf
  BaseMemoryLib|MdePkg/Library/BaseMemoryLib/BaseMemoryLib.inf
  SynchronizationLib|MdePkg/Library/BaseSynchronizationLib/BaseSynchronizationLib.inf
  PrintLib|MdePkg/Library/BasePrintLib/BasePrintLib.inf
  DebugLib|MdePkg/Library/UefiDebugLibStdErr/UefiDebugLibStdErr.inf
  DebugPrintErrorLevelLib|MdePkg/Library/BaseDebugPrintErrorLevelLib/BaseDebugPrintErrorLevelLib.inf
  PostCodeLib|MdePkg/Library/BasePostCodeLibPort80/BasePostCodeLibPort80.inf
  PcdLib|MdePkg/Library/BasePcdLibNull/BasePcdLibNull.inf
```
