<!--- @file
  7.11 Runtime Drivers

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

## 7.11 Runtime Drivers

UEFI Runtime Drivers are not common. If a UEFI Driver does not need to provide
services after `ExitBootServices()`, the UEFI Driver should not use the
techniques described in this section. The best example of a runtime driver
following the UEFI driver model is an UNDI driver providing services for a
network interface controller (NIC).

A UEFI Runtime Driver provides services that are available after
`ExitBootServices()` has been called. UEFI Drivers of this category are much
more difficult to implement and validate because they are required to execute
in both the pre-boot environment, where the system firmware owns the platform,
and in the post-boot environment, where an operating system owns the platform.

An OS may choose to execute in a virtual addressing mode and, as a result, may
prefer to call firmware services provided by UEFI Runtime Drivers in a virtual
addressing mode. A UEFI Runtime Driver must not make any assumptions about the
type of operating system to be booted, so the driver must always be able to
switch from using physical addresses to using virtual addresses if the operating system calls
`SetVirtualAddressMap()`.

In addition, because all memory regions marked as boot services memory in the
UEFI memory map are converted to available memory when the OS boots, a UEFI
Runtime Driver must allocate memory buffers required by the services provided after
`ExitBootServices()` in order to be allocated from runtime memory.

A UEFI Runtime Driver typically creates the following two events so the driver
is notified when these important transitions occur:

* Exit Boot Services event
* Set Virtual Address Map event

The Exit Boot Services event is signaled when the OS loader or OS kernel calls
`ExitBootServices()`. After this point, the UEFI driver is not allowed to use
any of the UEFI boot services. The UEFI runtime services and services from other runtime
drivers are still available.

The Set Virtual Address Map event is signaled when the OS loader or OS kernel
calls `SetVirtualAddressMap()`. If this event is signaled, the OS loader or OS
kernel requests that all runtime components be converted from their physical
address mapping to the virtual address mappings that are then passed to
`SetVirtualAddressMap()`.

The UEFI firmware below the UEFI Driver performs most of the work here by
relocating all the UEFI images from their physically addressed code and data
segments to their virtually addressed code and data segments. However, the UEFI
firmware below the UEFI Driver is not aware of runtime memory buffers have been
allocated by a UEFI Runtime Driver. UEFI firmware below the UEFI Driver is also
not aware if there are any pointer values within those allocated buffers that
must be converted from physical addresses to virtual addresses.

**********
**Caution:** The notification function for the Set Virtual Address Map event is
required to use the
**********

_UEFI Runtime Service `ConvertPointer()` to convert all pointers in global
variables and allocated runtime buffers from physical address to virtual
addresses. This code may be complex and difficult to get correct because, at
this time, no tools are available to help know when all the pointers have been
converted. When not done correctly, the only symptom noticed may be that the OS
crashes or hangs due to a condition in the middle of a call to a service
produced by a runtime driver._

**********
**Note:** _The algorithm to convert pointers can be especially complex if the
UEFI_
**********

_Runtime Driver manages linked lists or nested structures. The `SetVirtualAddressMap()` event executes in physical mode, so all linked list and structure traversals must be performed with the physical versions of the
pointer values. Once a pointer value is converted from a physical address to a
virtual address, that pointer value cannot be used again within the
`SetVirtualAddressMap()` event. The typical approach is to convert the
pointers to the leaf structures first and work towards the root.

The following example shows the driver entry point for a UEFI Runtime Driver
that creates an Exit Boot Services event and a Set Virtual Address Map event.
These events are typically declared as global variables. The notification
function for the Exit Boot Services event sets a global variable `gAtRuntime`
to `TRUE`, allowing the code in other functions to know if the UEFI boot
services are available or not. This global variable is initialized to `FALSE`
in its declaration. The notification function for the Set Virtual Address Map
event converts one global pointer from a physical address to a virtual address
as an example using a the `EfiConvertPointer()` function from the EDK II
library `UefiRuntimeLib`. A real driver might have many more pointers to
convert. In general, a UEFI Runtime Driver should be designed to reduce or
eliminate pointers that need to be converted to minimize the likelihood of
missing a pointer conversion.

###### Example 108-UEFI Runtime Driver entry point

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/UefiRuntimeLib.h>
#include <Library/DebugLib.h>

//
// Global variable for Exit Boot Services event.
//
EFI_EVENT mExitBootServicesEvent = NULL;

//
// Global variable for Set Virtual Address Map event.
//
EFI_EVENT mSetVirtualAddressMapEvent = NULL;

//
// Global variable updated when Exit Boot Services is signaled.
//
BOOLEAN gAtRuntime = FALSE;

//
// Global pointer that is converted to a virtual address when
// Set Virtual Address Map is signaled.
//
VOID *gGlobalPointer;

VOID
EFIAPI
AbcNotifyExitBootServices (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  gAtRuntime = TRUE;
}

VOID
EFIAPI
AbcNotifySetVirtualAddressMap (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  EFI_STATUS  Status;
  Status = EfiConvertPointer (
             EFI_OPTIONAL_PTR,
             (VOID **)&gGlobalPointer
             );
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
                  EVT_SIGNAL_EXIT_BOOT_SERVICES,     // Type
                  TPL_NOTIFY,                        // NotifyTpl
                  AbcNotifyExitBootServices,         // NotifyFunction
                  NULL,                              // NotifyContext
                  &mExitBootServicesEvent            // Event
                  );
  ASSERT_EFI_ERROR (Status);
  
  //
  // Create a Set Virtual Address Map event.
  //
  Status = gBS->CreateEvent (
                  EVT_SIGNAL_VIRTUAL_ADDRESS_CHANGE, // Type
                  TPL_NOTIFY,                        // NotifyTpl
                  AbcNotifySetVirtualAddressMap,     // NotifyFunction
                  NULL,                              // NotifyContext
                  &mSetVirtualAddressMapEvent        // Event
                  );
  ASSERT_EFI_ERROR (Status);
  
  //
  // Perform additional driver initialization here
  //
  
  return EFI_SUCCESS;
}
```

A UEFI Runtime Driver must have the required subsystem type in the PE/COFF
image for the UEFI Boot Service `LoadImage()` to allocate memory for the code
and data sections from runtime memory. In the EDK II this is done by setting
`MODULE_TYPE` in the `[Defines]` section of the INF file to
`DXE_RUNTIME_DRIVER`. In addition, a `MODULE_TYPE` of `DXE_RUNTIME_DRIVER` is
required to have a `[Depex]` section in the INF file. UEFI Runtime Driver must
use the same `[Depex]` section contents. The _example below_ shows the INF file
for a UEFI Runtime Driver with a `MODULE_TYPE` of `DXE_RUNTIME_DRIVER` and the
required `[Depex]` section.

###### Example 109-UEFI Runtime Driver INF File

```ini
[Defines]
  INF_VERSION    = 0x00010005
  BASE_NAME      = AbcRuntimeDriver
  FILE_GUID      = D3A3F14B-8ED4-438c-B7B7-FAF3F639B160
  MODULE_TYPE    = DXE_RUNTIME_DRIVER
  VERSION_STRING = 1.0
  ENTRY_POINT    = AbcDriverEntryPoint

[Sources]
  Abc.c

[Packages]
  MdePkg/MdePkg.dec

[LibraryClasses]
  UefiDriverEntryPoint
  UefiBootServicesTableLib
  UefiRuntimeLib
  DebugLib

[Depex]
  gEfiBdsArchProtocolGuid AND
  gEfiCpuArchProtocolGuid AND
  gEfiMetronomeArchProtocolGuid AND
  gEfiMonotonicCounterArchProtocolGuid AND
  gEfiRealTimeClockArchProtocolGuid AND
  gEfiResetArchProtocolGuid AND
  gEfiRuntimeArchProtocolGuid AND
  gEfiSecurityArchProtocolGuid AND
  gEfiTimerArchProtocolGuid AND
  gEfiVariableWriteArchProtocolGuid AND
  gEfiVariableArchProtocolGuid AND
  gEfiWatchdogTimerArchProtocolGuid
```

If a UEFI Runtime Driver also supports the unload feature, the `Unload()`
function must close the Exit Boot Services and Set Virtual Address Map events
by calling the UEFI Boot Service `CloseEvent()`. These events are typically
declared as global variables so they can be easily accessed from the `Unload()`
function. The example below shows an unloadable runtime driver. It is the same
as the previous example, except the entry point looks up the
`EFI_LOADED_IMAGE_PROTOCOL` associated with _ImageHandle_ and registers the
Unload() function called `AbcUnload()`. `AbcUnload()` closes the events that
were created in the driver entry point using the UEFI Boot Service
`CloseEvent()`.

###### Example 110-UEFI Runtime Driver entry point with Unload feature

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/UefiRuntimeLib.h>
#include <Library/DebugLib.h>

//
// Global variable for Exit Boot Services event.
//
EFI_EVENT mExitBootServicesEvent = NULL;

//
// Global variable for Set Virtual Address Map event.
//
EFI_EVENT mSetVirtualAddressMapEvent = NULL;

//
// Global variable updated when Exit Boot Services is signaled.
//
BOOLEAN gAtRuntime = FALSE;

//
// Global pointer that is converted to a virtual address when
// Set Virtual Address Map is signaled.
//
VOID *gGlobalPointer;

VOID
EFIAPI
AbcNotifyExitBootServices (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  gAtRuntime = TRUE;
}

VOID
EFIAPI
AbcNotifySetVirtualAddressMap (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  EFI_STATUS  Status;
  Status = EfiConvertPointer (
             EFI_OPTIONAL_PTR,
             (VOID **)&gGlobalPointer
             );
}

EFI_STATUS EFIAPI
AbcUnload (
  IN EFI_HANDLE  ImageHandle
  )
{
  gBS->CloseEvent (mExitBootServicesEvent);
  gBS->CloseEvent (mSetVirtualAddressMapEvent);
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
                  EVT_SIGNAL_EXIT_BOOT_SERVICES,     // Type
                  TPL_NOTIFY,                        // NotifyTpl
                  AbcNotifyExitBootServices,         // NotifyFunction
                  NULL,                              // NotifyContext
                  &mExitBootServicesEvent            // Event
                  );
  ASSERT_EFI_ERROR (Status);
  
  //
  // Create a Set Virtual Address Map event.
  //
  Status = gBS->CreateEvent (
                  EVT_SIGNAL_VIRTUAL_ADDRESS_CHANGE, // Type
                  TPL_NOTIFY,                        // NotifyTpl
                  AbcNotifySetVirtualAddressMap,     // NotifyFunction
                  NULL,                              // NotifyContext
                  &mSetVirtualAddressMapEvent        // Event
                  );
  ASSERT_EFI_ERROR (Status);
  
  //
  // Perform additional driver initialization here
  //
  return EFI_SUCCESS;
}
```
