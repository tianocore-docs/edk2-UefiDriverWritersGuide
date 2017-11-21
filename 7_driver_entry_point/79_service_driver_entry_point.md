<!--- @file
  7.9 Service Driver entry point

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

## 7.9 Service Driver entry point

A service driver produces one or more protocol interfaces on the driver's image
handle or on newly created handles. The example below, shows the Decompress
Protocol being installed onto the driver's image handle. A service driver may
produce an `Unload()` service, and that service would be required to uninstall
the protocols that were installed in the driver's entry point.

**********
**Caution:** The `Unload()` service for a service driver may be a dangerous
operation because there is no way for the service driver to know if the
protocols that it installed are being used by other UEFI components. If the
service driver is unloaded and other UEFI components are still using the
protocols that were produced by the unloaded driver, then the system is likely
to fail.
**********

###### Example 104-Service driver entry point using image handle

```c
#include <Uefi.h>
#include <Protocol/Decompress.h>
#include <Library/UefiBootServicesTableLib.h>

//
// Decompress Protocol instance
//
EFI_DECOMPRESS_PROTOCOL gAbcDecompress = {
  AbcGetInfo,
  AbcDecompress
};

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  //
  // Install Decompress Protocol onto UEFI Driver's ImageHandle
  //
  return gBS->InstallMultipleProtocolInterfaces (
                &ImageHandle,
                &gEfiDecompressProtocolGuid,
                &gAbcDecompress,
                NULL
                );
}
```

A service driver may also install its protocol interfaces onto one or more new
handles in the Handle Database. The following example shows a template for a
service driver called `Abc` that produces the Decompress Protocol on a new
handle.

###### Example 105-Service driver entry point creating new handle

```c
#include <Uefi.h>
#include <Protocol/Decompress.h>
#include <Library/UefiBootServicesTableLib.h>

//
// Handle for the Decompress Protocol
//
EFI_HANDLE gAbcDecompressHandle = NULL;

//
// Decompress Protocol instance
//
EFI_DECOMPRESS_PROTOCOL gAbcDecompress = {
  AbcGetInfo,
  AbcDecompress
};

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  //
  // Install Decompress Protocol onto a new handle
  //
  return gBS->InstallMultipleProtocolInterfaces (
                &gAbcDecompressHandle,
                &gEfiDecompressProtocolGuid,
                &gAbcDecompress,
                NULL
                );
}
```
