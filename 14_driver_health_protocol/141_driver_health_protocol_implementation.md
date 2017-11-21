<!--- @file
  14.1 Driver Health Protocol Implementation

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

## 14.1 Driver Health Protocol Implementation

The implementation of the Driver Health Protocol is typically found in the
file DriverHealth.c. Appendix A contains a template for a DriverHealth.c file
for a UEFI Driver. The list of tasks to implement the Driver Health Protocol
feature follow:
* Add global variable for the `EFI_DRIVER_HEALTH_PROTOCOL` instance to
  `DriverHealth.c`.
* Add private fields, as required, to the design of the private context data
  structure that supports storing the current health status of a device and
  managing repair operations.
* Implement the `GetHealthStatus()` service of the Driver Health Protocol in
  `DriverHealth.c`.
* Implement the `Repair()` service of the Driver Health Protocol in
  `DriverHealth.c`.
* Install the Driver Health Protocol onto the same handle as that of the Driver
  Binding Protocol.
* If the UEFI Driver produces multiple Driver Binding Protocols, install the
  Driver Health Protocol on the same handles as those of the Driver Binding
  Protocol.
* If the UEFI Driver supports the unload feature, uninstall all the Driver
  Health Protocol instances in the `Unload()` function.

The example below shows the protocol interface structure for the Driver Health
Protocol for reference and is composed of two services; `GetHealthStatus()` and
`Repair()`.

###### Example 150-Driver Health Protocol

```c
typedef struct _EFI_DRIVER_HEALTH_PROTOCOL EFI_DRIVER_HEALTH_PROTOCOL;

///
/// When installed, the Driver Health Protocol produces a collection of services
/// that allow the health status for a controller to be retrieved. If a controller
/// is not in a usable state, status messages may be reported to the user, repair
/// operations can be invoked, and the user may be asked to make software and/or
/// hardware configuration changes.
///
struct _EFI_DRIVER_HEALTH_PROTOCOL {
  EFI_DRIVER_HEALTH_GET_HEALTH_STATUS GetHealthStatus;
  EFI_DRIVER_HEALTH_REPAIR Repair;
};
```

This example declares a global variable called `gAbcDriverHealth` with the
services `AbcGetHealthStatus()` and `AbcRepair()`. The UEFI Boot Service
`InstallMultipleProtocolInterfaces()` is used to install the Driver Health
Protocol instance `gAbcDriverHealth` onto the same _ImageHandle_ as that of the
Driver Binding Protocol instance `gAbcDriverBinding`

###### Example 151-Install Driver Health Protocol

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
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
EFI_DRIVER_HEALTH_PROTOCOL gAbcDriverHealth = {
  AbcGetHealthStatus,
  AbcRepair
};

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE                  ImageHandle,
  IN EFI_SYSTEM_TABLE            *SystemTable
  )
{
  EFI_STATUS  Status;
  //
  // Install driver model protocol(s) on ImageHandle
  //
  Status = EfiLibInstallDriverBinding (
             ImageHandle,        // ImageHandle
             SystemTable,        // SystemTable
             &gAbcDriverBinding, // DriverBinding
             ImageHandle         // DriverBindingHandle
             );
  ASSERT_EFI_ERROR (Status);
  
  //
  // Install Driver Family Override Protocol onto ImageHandle
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
