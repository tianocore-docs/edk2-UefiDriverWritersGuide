<!--- @file
  8.4 Freeing private context data structures

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

## 8.4 Freeing private context data structures

The private context data structures are freed in the `Stop()` function of the
driver's Driver Binding Protocol. The service typically used to free the private context
data structures is `FreePool()` from the EDK II library `MemoryAllocationLib`.

Shown below is a generic template for freeing a private context data structure
in the `Stop()`function of the Driver Binding Protocol. This code example shows
only a fragment from the `Stop()` service. _Chapter 9_ covers the services that
are produced by the Driver Binding Protocol in more detail.

###### Example 118-Free a private context data structure

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/MemoryAllocationLib.h>

EFI_STATUS
EFIAPI
    <<DriverName>>DriverBindingStop (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN UINTN                        NumberOfChildren,
  IN EFI_HANDLE                   *ChildHandleBuffer
  )
{
  EFI_STATUS  Status;
  EFI_<<PROTOCOL_NAME_Pm>>_PROTOCOL * <<ProtocolNamePm>>;
  <<DRIVER_NAME>>_PRIVATE_DATA Private;
  
  //
  // Look up one of the driver's produced protocols
  //
  Status = gBS->OpenProtocol (
                  ControllerHandle,
                  &gEfi <<ProtocolNamePm>>ProtocolGuid,
                  (VOID **)& <<ProtocolNamePm>>,
                  This->DriverBindingHandle,
                  ControllerHandle,
                  EFI_OPEN_PROTOCOL_GET_PROTOCOL
                  );
  if (EFI_ERROR (Status)) {
    return EFI_UNSUPPORTED;
  }
  
  //
  // Retrieve the private context data structure from the
  // produced protocol
  //
  Private = <<DRIVER_NAME > _PRIVATE_DATA_FROM_ <<PROTOCOL_NAME_Pm>>_THIS (
                <<ProtocolNamePm>>
                );
				
  //
  // Free the private context data structure
  //
  FreePool (Private);
  
  return Status;
}
```

The following example shows a code fragment from the `DiskIoDxe` driver in the
`MdeModulePkg` that frees the private context data structure.

###### Example 119-Disk I/O free of a private context data structure

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/DiskIo.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/MemoryAllocationLib.h>

EFI_STATUS
EFIAPI
DiskIoDriverBindingStop (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN UINTN                        NumberOfChildren,
  IN EFI_HANDLE                   *ChildHandleBuffer
  )
{
  EFI_STATUS                      Status;
  EFI_DISK_IO_PROTOCOL            *DiskIo;
  DISK_IO_PRIVATE_DATA            *Private;
  
  //
  // Get our context back.
  //
  Status = gBS->OpenProtocol (
                  ControllerHandle,
                  &gEfiDiskIoProtocolGuid,
                  (VOID **)&DiskIo,
                  This->DriverBindingHandle,
                  ControllerHandle,
                  EFI_OPEN_PROTOCOL_GET_PROTOCOL
                  );
  if (EFI_ERROR (Status)) {
    return EFI_UNSUPPORTED;
  }
  Private = DISK_IO_PRIVATE_DATA_FROM_THIS (DiskIo);
  
  FreePool (Private);
  
  return Status;
}
```
