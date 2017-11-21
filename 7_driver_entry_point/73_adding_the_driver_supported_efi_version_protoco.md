<!--- @file
  7.3 Adding the Driver Supported EFI Version Protocol Feature

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

## 7.3 Adding the Driver Supported EFI Version Protocol Feature

This feature provides information on the version of the _UEFI Specification_ to
which the UEFI Driver conforms. The version information follows the same format
as the version field in the EFI System Table. This feature is required for UEFI
Drivers on PCI and other plug in cards.

There are no EDK II library functions to help install the Driver Supported EFI
Version Protocol. Instead, the UEFI Driver requiring this feature must install the
Driver Supported EFI Version Protocol using the UEFI Boot Service
`InstallMultipleProtocolInterfaces()`. A UEFI Driver must install, at most, one
instance of this protocol and, if it is produced, it must be installed onto the
_ImageHandle_. This protocol is composed of only data fields, so no functions
need be implemented to complete its implementation. Example 94, below, expands
Example 93, above, and adds a Driver Supported EFI Version Protocol instance to
_ImageHandle_. The Driver Supported EFI Version Protocol instance in this
example specifies that this UEFI Driver follows the _UEFI 2.3.1 Specification_.

###### Example 94-Driver Supported EFI Version Protocol Feature

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/ComponentName.h>
#include <Protocol/DriverSupportedEfiVersion.h>
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
EFI_DRIVER_SUPPORTED_EFI_VERSION_PROTOCOL gAbcDriverSupportedEfiVersion = {
  sizeof (EFI_DRIVER_SUPPORTED_EFI_VERSION_PROTOCOL),
  EFI_2_31_SYSTEM_TABLE_REVISION
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
  // Install Driver Supported EFI Version Protocol onto ImageHandle
  //
  Status = gBS->InstallMultipleProtocolInterfaces (
                  &ImageHandle,
                  &gEfiDriverSupportedEfiVersionProtocolGuid,
                  & gAbcDriverSupportedEfiVersion,
                  NULL
                  );
  ASSERT_EFI_ERROR (Status);
  
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
  
  return Status;
}
```
