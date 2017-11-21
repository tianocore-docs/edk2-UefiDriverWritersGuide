<!--- @file
  7.2.3 Adding Driver Health Protocol Feature

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

### 7.2.3 Adding Driver Health Protocol Feature

The Driver Health Protocol provides services allowing a UEFI Driver to express
the health status of a controller, return status messages associated with the
health status, perform repair operations and request configuration changes
required to place the controller in a usable state. This protocol is required
only for devices that may be in a bad state which can be recovered through a
repair operation or a configuration change. If a device can never be in a bad
state, or a device can be in a bad state for which there is no recovery
possible, this protocol should not be installed.

There are no EDK II library functions to help install the Driver Health
Protocol. Instead, the UEFI Driver that requires this feature must install the
Driver Health Protocol using the UEFI Boot Service
`InstallMultipleProtocolInterfaces()`. Example 92, below, expands Example 91,
above, and adds a Driver Health Protocol instance to _ImageHandle_, the same
handle on which the Driver Binding Protocol is installed.

###### Example 92-Driver Heath Protocol Feature

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/ComponentName.h>
#include <Protocol/DriverHealth.h>
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
EFI_COMPONENT_NAME2_PROTOCOL gAbcComponentName2 = {
  AbcGetDriverName,
  AbcGetControllerName,
  "en"
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_DRIVER_HEALTH_PROTOCOL gAbcDriverHealth = {
  AbcGetHealthStatus,
  AbcRepair
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
             ImageHandle,                 // ImageHandle
             SystemTable,                 // SystemTable
             &gAbcDriverBinding,          // DriverBinding
             ImageHandle,                 // DriverBindingHandle
             &gAbcComponentName,          // ComponentName
             &gAbcComponentName2          // ComponentName2
             );
  ASSERT_EFI_ERROR (Status);
  
  //
  // Install Driver Health Protocol onto ImageHandle
  //
  Status = gBS->InstallMultipleProtocolInterfaces (
                  &ImageHandle,
                  &gEfiDriverHealthProtocolGuid,
                  &gAbcDriverHealth,
                  NULL
                  );
  ASSERT_EFI_ERROR (Status);
  
  return Status;
}
```
