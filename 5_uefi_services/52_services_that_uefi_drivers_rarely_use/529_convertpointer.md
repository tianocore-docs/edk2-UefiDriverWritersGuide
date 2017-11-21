<!--- @file
  5.2.9 ConvertPointer()

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

### 5.2.9 `ConvertPointer()`

_UEFI Boot Service drivers must never use this service._

This service may be required by UEFI _Runtime_ Drivers if the UEFI Runtime
Driver is required to convert pointer values that use physical addresses to
pointer values that use virtual addresses. A UEFI Runtime driver must only call
`ConvertPointer()` from an event notification function for an event of type
`EVT_SIGNAL_VIRTUAL_ADDRESS_CHANGE` or a GUIDed event of type
`EFI_EVENT_GROUP_VIRTUAL_ADDRESS_CHANGE`.

**********
**Caution:** Notification functions for events signaled when
SetVirtualAddressMap() is called by an OS Loader or OS Kernel are not allowed
to use any of the UEFI boot services, UEFI Console Services, or UEFI Protocol
Services either directly or indirectly because those services are no longer
available when `SetVirtualAddressMap()` is called. Instead, this type of
notification function typically uses `ConvertPointer()` to convert pointers
within data structures that are managed by the UEFI runtime driver from
physical addresses to virtual addresses.
**********

UEFI system firmware takes care of most of the physical to virtual address
translations that a UEFI Runtime Driver requires. For example, all of the code
and data sections in the UEFI Runtime Driver image are automatically fixed up
for proper execution at the virtual address ranges provided by the operating
system when the operating system calls the UEFI Runtime Service
`SetVirtualAddressMap()`.

If a UEFI Runtime Driver caches pointer values in global variables, or a UEFI
Runtime Driver allocates buffers from `EfiRuntimeServicesData`, those pointer
values must be converted from physical addresses to virtual address using the
virtual address ranges provided by the operating system when the operating
system calls the UEFI Runtime Service `SetVirtualAddressMap()`. If allocated
buffers contain more pointers, then those pointer values must also be converted.

In these more complex scenarios, the order of the conversions is critical
because the algorithm in the UEFI Runtime Driver must guarantee that no virtual
addresses in the execution of the notification actually function because the
event notification function on `SetVirtualAddressMap()` only executes in
physical mode.

The following code fragment shows how a UEFI Runtime Driver can create an event
whose notification function is executed in physical mode when the OS Loader or
OS Kernel calls `SetVirtualAddressMap()`. There are two methods to create a
`SetVirtualAddressMap()` event. This example shows the preferred method that
uses `CreateEventEx()` to pass in the GUID of `gEfiEventVirtualAddressChangeGuid`.
The alternate method uses `CreateEvent()` or `CreateEventEx()` with an event type
of `EVT_SIGNAL_VIRTUAL_ADDRESS_CHANGE`. The created event is declared as a global
variable. and makes the event available if the UEFI Runtime Driver needs to close
the event if UEFI Runtime Driver is unloaded. The code fragments that follow this
example show how `ConvertPointer()` may be used from `NotifySetVirtualAddressMap()`,
the event notification function from this example.

###### Example 74-Create a Set Virtual Address Map event

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

#include <Guid/EventGroup.h>

//
// Global variable for the SetVirtualAddressMap event
//
EFI_EVENT mSetVirtualAddressMapEvent = NULL;

EFI_STATUS Status;

//
// Create a Set Virtual Address Map event.
//
Status = gBS->CreateEventEx (
                EVT_NOTIFY_SIGNAL,                   // Type
                TPL_NOTIFY,                          // NotifyTpl
                NotifySetVirtualAddressMap,          // NotifyFunction
                NULL,                                // NotifyContext
                &gEfiEventVirtualAddressChangeGuid,  // EventGroup
                &mSetVirtualAddressMapEvent          // Event
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

The following code fragment shows how `ConvertPointer()` is used to convert a
global variable functioning as a pointer from a physical address to that with a
virtual address.

The flag `EFI_OPTIONAL_PTR` tells `ConvertPointer()` to not perform a
conversion if the physical address of the pointer is `NULL`. This is useful if
it is legal for some of the pointer values to be `NULL` and the `NULL` value
needs to be preserved after the conversion. The only other legal value for this
field is 0 The conversion should be performed unconditionally.

###### Example 75-Convert a global pointer from physical to virtual

```c
#include <Uefi.h>
#include <Library/UefiRuntimeServicesTableLib.h>

VOID *gGlobalPointer;

VOID
EFIAPI
NotifySetVirtualAddressMap (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  EFI_STATUS  Status;
  Status = gRT->ConvertPointer (
                  EFI_OPTIONAL_PTR,
                  (VOID **)&gGlobalPointer
                  );
}
```

The code fragment in Example 76, below, is identical to 75, above, but uses the
function `EfiConvertPointer()` from the EDK II library `UefiRuntimeLib` to call
the UEFI Runtime Service `ConvertPointer()`.

```c
#include <Uefi.h>
#include <Library/UefiRuntimeLib.h>

VOID *gGlobalPointer;

VOID
EFIAPI
NotifySetVirtualAddressMap (
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
```

###### Example 76-Using UefiRuntimeLib to convert a pointer

The EDK II library `UefiRuntimeLib` also provides the function
`EfiConvertFunctionPointer()` to convert a function pointer from a physical
address to a virtual address. On supported CPU architectures where there is no
distinction between a data pointer and a function pointer, `EfiConvertPointer()`
and `EfiConvertFunctionPointer()` are identical. On other CPU architectures such as
IPF, where function calls are made through a `PLABEL`, converting a function
pointer is more complex. The EDK II library `UefiRuntimeLib` helps hide these
CPU specific details so the UEFI Driver sources can be the same for all
supported CPU architectures.

Since the UEFI system firmware automatically converts functions in code
sections of a UEFI Runtime Driver image from physical addresses to virtual
addresses, `EfiConvertFunctionPointer()` is required only if a UEFI Driver caches a
function pointer in a global variable or an allocated buffer.

###### Example 77-Using UefiRuntimeLib to convert a function pointer

```c
#include <Uefi.h>
#include <Library/UefiRuntimeLib.h>

typedef
VOID
(EFIAPI *EFI_EXAMPLE_FUNCTION)(
  IN VOID *Context
);

EFI_EXAMPLE_FUNCTION gGlobalFunctionPointer;

VOID
EFIAPI
NotifySetVirtualAddressMap (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  EFI_STATUS  Status;
  
  Status = EfiConvertFunctionPointer (
             EFI_OPTIONAL_PTR,
             (VOID **)&gGlobalFunctionPointer
             );
}
```

The EDK II library `UefiRuntimeLib` also provides helper function call
`EfiConvertList()` to convert all the pointer values in a doubly linked list of
type `LIST_ENTRY`. All the nodes in the linked list are traversed and the
forward and backward link in each node is converted from a physical address to
a virtual address.

Once this conversion is performed, the linked list cannot be accessed again in
this function because all the pointer values are now virtual addresses. If the
contents of the linked list contain structures with more pointer values that
also need to be converted, those conversions must be performed prior to calling
`EfiConvertList()`.

###### Example 78-Using UefiRuntimeLib to convert a linked list

```c
#include <Uefi.h>
#include <Library/UefiRuntimeLib.h>

LIST_ENTRY gGlobalList = INITIALIZE_LIST_HEAD_VARIABLE (gGlobalList);

VOID
EFIAPI
NotifySetVirtualAddressMap (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  EFI_STATUS  Status;
  
  Status = EfiConvertList (EFI_OPTIONAL_PTR, &gGlobalList);
}
```
