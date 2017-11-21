<!--- @file
  7.6 Adding the Unload Feature

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

## 7.6 Adding the Unload Feature

Any UEFI driver can be made unloadable. This feature is useful for some driver
categories, but it may not be useful at all for other driver categories. It
does not make sense to add the unload feature to an initializing driver because
this category of driver already returns an error from the driver entry point,
which forces the UEFI Image Services to automatically unload the initializing
driver.

Similarly, it usually doesn't make sense for root bridge drivers or service
drivers to add the unload feature. These categories of driver typically produce
protocols consumed by other UEFI drivers to produce basic console functions and
boot device abstractions. If a root bridge driver or a service driver is
unloaded, any UEFI driver using the protocols from those drivers would start to
fail. If a root bridge driver or service driver guarantees that it is not being
used by any other UEFI components, they may be unloaded without any adverse
side effects.

Still, the `Unload()` function can be very helpful. It allows the "unload"
command in the UEFI Shell to completely remove a UEFI driver image from memory
and remove all of the driver's handles and protocols from the handle database.
If a driver is suspected of causing a bug, it is often helpful to "unload" the
driver from the UEFI Shell and then run tests knowing that the driver is no
longer present in the platform. In these cases, the Unload() feature is
superior to simply stopping the driver with the disconnect UEFI Shell command.
If a driver is just disconnected, the UEFI Shell commands "connect" and
"reconnect" may inadvertently restart the driver.

The unload feature is also very helpful when testing and developing new
versions of the driver. The old version can be completely unloaded (removed
from the system) and new versions of the driver, even those having the same
version number, can safely be installed in the system without concern the older
version of the driver may be invoked during the next connect or reconnect
operation.

Be mindful that, because `Unload()` completely removes the driver from system
memory, it might not be possible to load it back into the system in the same
session. For example, if the driver is stored in system firmware or in a PCI
option ROM, no method may be available for reloading the driver without
completely rebooting the system.

The `Unload()` service is one of the fields in the `EFI_LOADED_IMAGE_PROTOCOL`.
This protocol is automatically created and installed when a UEFI image is
loaded with the EFI Boot Service `LoadImage()`. When the
`EFI_LOADED_IMAGE_PROTOCOL` is created by `LoadImage()`, the `Unload()` service
is initialized to `NULL`. It is the driver entry point's responsibility to
register the `Unload()` function in the `EFI_LOADED_IMAGE_PROTOCOL`.

It is `recommended` that UEFI drivers following the UEFI driver model add the
unload feature. It is very useful during driver development, driver debug, and
system integration. It is strongly recommended that this service remain in
drivers for add-in adapters to help debug interaction issues during system
integration.

Example 98, below, shows the same driver entry point from _Example 89_ (earlier
in this section) with the unload feature added. Example 98 shows only a
template for the `Unload()` function because the implementation of this service
varies from driver to driver. The `Unload()` service is responsible for
cleaning up everything the driver has done since initialization. This
responsibility means that the `Unload()` service should do the following:

* Free any resources that were allocated.

* Remove any protocols that were added.

* Destroy any handles that were created.

If the `Unload()` service does not want to unload the driver at the time the
`Unload()` service is called, it may return an error and not unload the driver.
The only way a driver can actually be unloaded is by ensuring that the
`Unload()` service has been registered in the `EFI_LOADED_IMAGE_PROTOCOL` and
that it returns `EFI_SUCCESS`.

###### Example 98-Add the Unload feature

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/ComponentName.h>
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

EFI_STATUS EFIAPI
AbcUnload (
  IN EFI_HANDLE  ImageHandle
  )
{
  return EFI_SUCCESS;
}

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE                     ImageHandle,
  IN EFI_SYSTEM_TABLE               *SystemTable
  )
{
  EFI_STATUS  Status;
  
  //
  // Install driver model protocol(s).
  //
  Status = EfiLibInstallDriverBindingComponentName2 (
             ImageHandle,           // ImageHandle
             SystemTable,           // SystemTable
             &gAbcDriverBinding,    // DriverBinding
             ImageHandle,           // DriverBindingHandle
             &gAbcComponentName,    // ComponentName
             &gAbcComponentName2    // ComponentName2
             );
  ASSERT_EFI_ERROR (Status);
  
  return Status;
}
```

The EDK II provides an easy method to declare the name of the UEFI driver-specific
`Unload()` function in the `[Defines]` section of the INF file for the UEFI
Driver. Example 99, below, shows the INF file that defines `UNLOAD_IMAGE` to
the `AbcUnload()` function shown in the previous example. The specified
`Unload()` function automatically registers in the `EFI_LOADED_IMAGE_PROTOCOL`
before the entry point of the UEFI Driver is called. See _Section 30.3_ for
more details on UEFI Driver INF files and _Appendix A_ for a complete template
of the INF file for a UEFI Driver.

###### Example 99-UEFI Driver INF File with Unload feature

```ini
[Defines]
  INF_VERSION    = 0x00010005
  BASE_NAME      = Abc
  FILE_GUID      = DA87D340-15C0-4824-9BF3-D52286674BEF
  MODULE_TYPE    = UEFI_DRIVER
  VERSION_STRING = 1.0
  ENTRY_POINT    = AbcDriverEntryPoint
  UNLOAD_IMAGE   = AbcUnload

[Sources]
  Abc.c

[Packages]
  MdePkg/MdePkg.dec

[LibraryClasses]
  UefiDriverEntryPoint
  UefiBootServicesTableLib
  UefiLib
  DebugLib
  MemoryAllocationLib
```

Example 100, below, shows one possible implementation of the `Unload()`
function for a UEFI driver following the UEFI driver model. It finds all the
devices it manages and disconnects the driver from those devices. Next, the
protocol interfaces installed in the driver entry point must be removed. The
example shown here matches the driver entry point from _Example 98_, above.
There are many possible algorithms that can be implemented in the `Unload()`
service. A driver may choose to be unloadable if, and only if, it is not
managing any devices at all. A driver may also choose to keep track of the
devices it is managing internally so it can selectively disconnect itself from
those devices when it is unloaded.

###### Example 100-UEFI Driver Model Unload Feature

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/ComponentName.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/MemoryAllocationLib.h>

EFI_STATUS EFIAPI
AbcUnload (
  IN EFI_HANDLE  ImageHandle
  )
{
  EFI_STATUS  Status;
  EFI_HANDLE  *HandleBuffer;
  UINTN       HandleCount;
  UINTN       Index;
  
  //
  // Retrieve array of all handles in the handle database
  //
  Status = gBS->LocateHandleBuffer (
                  AllHandles,
                  NULL,
                  NULL,
                  &HandleCount,
                  &HandleBuffer
                  );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // Disconnect the current driver from handles in the handle database
  //
  for (Index = 0;
       Index < HandleCount; Index++) {
    Status = gBS->DisconnectController (
                    HandleBuffer[Index],
                    gImageHandle,
                    NULL
                    );
  }
  
  //
  // Free the array of handles
  //
  FreePool (HandleBuffer);
  
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
  // Do any additional cleanup that is required for this driver
  //
  
  return EFI_SUCCESS;
}
```
