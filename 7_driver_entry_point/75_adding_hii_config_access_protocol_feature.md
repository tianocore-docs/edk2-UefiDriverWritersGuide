<!--- @file
  7.5 Adding HII Config Access Protocol Feature

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

## 7.5 Adding HII Config Access Protocol Feature

This protocol provides the services to save and restore configuration settings
for a device. For drivers following the UEFI Driver Model, this protocol is
typically installed in the Driver Binding Protocol `Start()` function for each
device the driver manages. Only UEFI Drivers not following the UEFI Driver
Model would install this protocol in the driver entry point. As a result, only
the Service Drivers and Root Bridge Drivers required to save and restore
configuration settings can install the HII Config Access Protocol in the driver
entry point.

There are no EDK II library functions to help install the HII Config Access
Protocol. Instead, the UEFI Driver requiring this feature must install the HII Config
Access Protocol using the UEFI Boot Service `InstallMultipleProtocolInterfaces()`.
Example 97, below, expands Example 96, above, and adds an HII Config Access
Protocol instance to _ImageHandle_.

###### Example 97-HII Config Access Protocol Feature

```c
#include <Uefi.h>
#include <Protocol/HiiConfigAccess.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/DebugLib.h>

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_HII_CONFIG_ACCESS_PROTOCOL gAbcHiiConfigAccess = {
  AbcExtractConfig,
  AbcRouteConfig,
  AbcRouteCallback
};

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  EFI_STATUS  Status;
  
  //
  // Install HII Config Access Protocol onto ImageHandle
  //
  Status = gBS->InstallMultipleProtocolInterfaces (
                  &ImageHandle,
                  &gEfiHiiConfigAccessProtocolGuid,
                  &gAbcHiiConfigAccess,
                  NULL
                  );
  ASSERT_EFI_ERROR (Status);
  
  return Status;
}
```
