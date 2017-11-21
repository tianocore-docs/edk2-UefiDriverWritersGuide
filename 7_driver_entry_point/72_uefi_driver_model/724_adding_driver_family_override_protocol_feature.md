<!--- @file
  7.2.4 Adding Driver Family Override Protocol Feature

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

### 7.2.4 Adding Driver Family Override Protocol Feature

The `EFI_DRIVER_FAMILY_OVERRIDE_PROTOCOL` is optional. It is typically produced
by UEFI Drivers associated with a set of similar controllers where multiple versions of
a UEFI Driver for the set of similar controllers may be simultaneously present in a
platform. This protocol allows each UEFI Driver to advertise a version number such that
the UEFI Driver with the highest version is selected to manage all the
controllers in the set of similar controllers.

`PCI Use Case`: If a platform has 3 PCI SCSI adapters from the same
manufacturer, and the manufacturer requires the PCI SCSI adapter having the
highest version UEFI Driver to manage all 3 PCI SCSI adapters, the Driver
Family Override Protocol is required and provides the version value used to
make the selection. If the Driver Family Override Protocol is not produced, the
Bus Specific Driver Override Protocol for PCI selects the UEFI Driver from the
adapter's PCI Option ROM to manage each adapter.

There are no EDK II library functions to help install the Driver Family
Override Protocol. Instead, the UEFI Driver requiring this feature must install
the Driver Family Override Protocol using the UEFI Boot Service
`InstallMultipleProtocolInterfaces()`. Example 93, below, expands Example 92, above, and adds a Driver Family Override
Protocol instance to _ImageHandle_, the same handle on which the Driver Binding
Protocol is installed.

###### Example 93-Driver Family Override Protocol Feature

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/ComponentName.h>
#include <Protocol/DriverFamilyOverride.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/UefiLib.h>
#include <Library/DebugLib.h>

#define ABC_VERSION 0x10

EFI_DRIVER_BINDING_PROTOCOL gAbcDriverBinding = {
  AbcSupported,
  AbcStart,
  AbcStop,
  ABC_VERSION,
  NULL,
  NULL
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME_PROTOCOL gAbcComponentName = {
  (EFI_COMPONENT_NAME_GET_DRIVER_NAME) AbcGetDriverName,
  (EFI_COMPONENT_NAME_GET_CONTROLLER_NAME) AbcGetControllerName,
  "eng"
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME2_PROTOCOL gAbcComponentName2 = { AbcGetDriverName,
                AbcGetControllerName,
                "en"
                };
 
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_DRIVER_FAMILY_OVERRIDE_PROTOCOL gAbcDriverFamilyOverride = {
  AbcGetVersion
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
  // Install driver model protocol(s) on ImageHandle
  //
  Status = EfiLibInstallDriverBindingComponentName2 (
             ImageHandle,                    // ImageHandle
             SystemTable,                    // SystemTable
             &gAbcDriverBinding,             // DriverBinding
             ImageHandle,                    // DriverBindingHandle
             &gAbcComponentName,             // ComponentName
             &gAbcComponentName2             // ComponentName2
             );
  ASSERT_EFI_ERROR (Status);
  
  //
  // Install Driver Family Override Protocol onto ImageHandle
  //
  Status = gBS->InstallMultipleProtocolInterfaces (
                  &ImageHandle,
                  &gEfiDriverFamilyOverrideProtocolGuid,
                  &gAbcDriverFamilyOverride,
                  NULL
                  );
  ASSERT_EFI_ERROR (Status);
  
  return Status;
}
```
