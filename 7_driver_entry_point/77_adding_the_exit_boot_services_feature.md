<!--- @file
  7.7 Adding the Exit Boot Services feature

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

## 7.7 Adding the Exit Boot Services feature

Some UEFI drivers may need to put their devices into a quiescent state or a
known state prior to booting an operating system. This case is considered to be
very rare because the OS-present drivers should not depend on a UEFI driver
running at all. Not depending on a running UEFI driver means that an OS-present
driver should be able to handle the following:

* A device in its power-on reset state

* A device that was recently hot added while the OS is running

* A device that was managed by a UEFI driver up to the point the OS was booted

* A device that was managed for a short period of time by a UEFI driver

In the rare case when a UEFI driver is required to place a device in a
quiescent or known state before booting an operating system, the driver can use
a special event type called an Exit Boot Services event. This event is signaled
when an OS loader or OS kernel calls the UEFI boot service
`ExitBootServices()`. This call is the point in time where the system firmware
still owns the platform, but the system firmware is just about to transfer
system ownership to the operating system. In this transition time, no
modifications to the UEFI memory map are allowed (see the Image Services
section of the _UEFI Specification_). This requirement means that the
notification function for an Exit Boot Services event is not allowed to
directly or indirectly allocate or free and memory through the UEFI memory
services.

Examples from the EDK II that use this feature are the PCI device drivers for
USB Host Controllers. Some USB Host Controllers are PCI Bus Masters that
continuously access a memory buffer to poll for operation requests. Access to
this memory buffer by a USB Host Controller may be required to boot an
operation system, but this activity must be terminated when the OS calls
`ExitBootServices()`. The typical action in the Exit Boot Services Event for
these types of drivers is to disable the PCI bus master and place the USB Host
Controller into a halted state

Example 101, below, shows the same example as in Example 100, above, but an Exit
Boot Services event is also created. The template for the notification function
for the Exit Boot Services event is also shown. This notification function
typically contains code to find the list of device handles that the driver is
currently managing, and it then performs operations on those handles to make
sure they are in the proper OS handoff state. Remember that no memory
allocation or free operations can be performed from this notification function.

###### Example 101-Adding the Exit Boot Services feature

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/ComponentName.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/UefiLib.h>
#include <Library/DebugLib.h>

#define ABC_VERSION 0x10

//
// Global variable for Exit Boot Services event.
//
EFI_EVENT mExitBootServicesEvent = NULL;

//
// Driver Binding Protocol Instance
//
EFI_DRIVER_BINDING_PROTOCOL gAbcDriverBinding = {
  AbcSupported,
  AbcStart,
  AbcStop,
  ABC_VERSION,
  NULL,
  NULL
};

//
// Component Name Protocol Instance
//
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME_PROTOCOL gAbcComponentName = {
  (EFI_COMPONENT_NAME_GET_DRIVER_NAME) AbcGetDriverName,
  (EFI_COMPONENT_NAME_GET_CONTROLLER_NAME) AbcGetControllerName,
  "eng"
};

//
// Component Name 2 Protocol Instance
//
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME2_PROTOCOL gAbcComponentName2 = {
  AbcGetDriverName,
  AbcGetControllerName,
  "en"
};

VOID
EFIAPI
AbcNotifyExitBootServices (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  //
  // Put driver-specific actions here.
  // No EFI Memory Service may be used directly or indirectly.
  //
}

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  EFI_STATUS  Status;
  
  //
  // Create an Exit Boot Services event.
  //
  Status = gBS->CreateEvent (
                  EVT_SIGNAL_EXIT_BOOT_SERVICES,    // Type
                  TPL_NOTIFY,                       // NotifyTpl
                  AbcNotifyExitBootServices,        // NotifyFunction
                  NULL,                             // NotifyContext
                  &mExitBootServicesEvent           // Event
                  );
  ASSERT_EFI_ERROR (Status);
  
  //
  // Install driver model protocol(s).
  //
  Status = EfiLibInstallDriverBindingComponentName2 (
             ImageHandle,                          // ImageHandle
             SystemTable,                          // SystemTable
             &gAbcDriverBinding,                   // DriverBinding
             ImageHandle,                          // DriverBindingHandle
             &gAbcComponentName,                   // ComponentName
             &gAbcComponentName2                   // ComponentName2
             );
  ASSERT_EFI_ERROR (Status);
  
  return Status;
}
```

If a UEFI driver supports both the unload feature and the Exit Boot Services
feature, the `Unload()` function must close the Exit Boot Services event by
calling `CloseEvent()`. This event is typically declared as a global variable
so it can be easily accessed from the `Unload()` function. The following
example is the same as the previous example, except the entry point looks up
the `EFI_LOADED_IMAGE_PROTOCOL` associated with _ImageHandle_ and registers the
`Unload()` function called `AbcUnload()`. `AbcUnload()` closes the event
created in the driver entry point using the UEFI Boot Service `CloseEvent()`.

###### Example 102-Add the Unload and Exit Boot Services event features

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/ComponentName.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/UefiLib.h>
#include <Library/DebugLib.h>

#define ABC_VERSION 0x10

//
// Global variable for Exit Boot Services event.
//
EFI_EVENT mExitBootServicesEvent = NULL;

//
// Driver Binding Protocol Instance
//
EFI_DRIVER_BINDING_PROTOCOL gAbcDriverBinding = {
  AbcSupported,
  AbcStart,
  AbcStop,
  ABC_VERSION,
  NULL,
  NULL
};

//
// Component Name Protocol Instance
//
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME_PROTOCOL gAbcComponentName = {
  (EFI_COMPONENT_NAME_GET_DRIVER_NAME) AbcGetDriverName,
  (EFI_COMPONENT_NAME_GET_CONTROLLER_NAME) AbcGetControllerName,
  "eng"
};

//
// Component Name 2 Protocol Instance
//
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME2_PROTOCOL gAbcComponentName2 = {
  AbcGetDriverName,
  AbcGetControllerName,
  "en"
};

VOID
EFIAPI
AbcNotifyExitBootServices (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  //
  // Put driver-specific actions here.
  // No EFI Memory Service may be used directly or indirectly.
  //
}

EFI_STATUS EFIAPI
AbcUnload (
  IN EFI_HANDLE   ImageHandle
  )
{
  EFI_STATUS      Status;
  
  //
  // Uninstall protocols installed in the driver entry point
  //
  Status = gBS->UninstallMultipleProtocolInterfaces (
                  ImageHandle,
                  &gEfiDriverBindingProtocolGuid,
                  &gAbcDriverBinding,
                  &gEfiComponentNameProtocolGuid,
                  &gAbcComponentName,
                  &gEfiComponentName2ProtocolGuid,
                  &gAbcComponentName2,
                  NULL
                  );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // Close Exit Boot Services event created in the driver entry point
  //
  gBS->CloseEvent (mExitBootServicesEvent);
  
  return EFI_SUCCESS;
}

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  EFI_STATUS  Status;
  //
  // Create an Exit Boot Services event.
  //
  Status = gBS->CreateEvent (
                  EVT_SIGNAL_EXIT_BOOT_SERVICES, // Type
                  TPL_NOTIFY,                    // NotifyTpl
                  AbcNotifyExitBootServices,     // NotifyFunction
                  NULL,                          // NotifyContext
                  &mExitBootServicesEvent        // Event
                  );
  ASSERT_EFI_ERROR (Status);
  
  //
  // Install driver model protocol(s).
  //
  Status = EfiLibInstallDriverBindingComponentName2 (
             ImageHandle,                        // ImageHandle
             SystemTable,                        // SystemTable
             &gAbcDriverBinding,                 // DriverBinding
             ImageHandle,                        // DriverBindingHandle
             &gAbcComponentName,                 // ComponentName
             &gAbcComponentName2                 // ComponentName2
             );
  ASSERT_EFI_ERROR (Status);
  
  return Status;
}
```
