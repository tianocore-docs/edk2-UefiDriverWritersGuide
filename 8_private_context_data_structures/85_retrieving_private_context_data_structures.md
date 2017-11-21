<!--- @file
  8.5 Retrieving private context data structures

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

## 8.5 Retrieving private context data structures

The protocol functions produced by a UEFI driver need to access the private
context data structure. These functions typically use the set of consumed
protocols and the private data fields to perform the protocol function's
required operation.

**Appendix A contains a template for a `<<ProtocolName>>`.c file for the
implementation of a protocol function that retrieves the private context data
structure using the CR() based macro and the This pointer for the produced
protocol.**

The following example shows a code fragment from the `ReadDisk()` service of
the `EFI_DISK_IO_PROTOCOL` that is produced by the `DiskIoDxe` driver in the
`MdeModulePkg`. It uses the `CR()` based macro called
`DISK_IO_PRIVATE_DATA_FROM_THIS()` and the _This_ pointer to the
`EFI_DISK_IO_PROTOCOL` to retrieve the `DISK_IO_PRIVATE_DATA` private context
data structure.

###### Example 120-Retrieving the Disk I/O private context data structure

```c
#include <Uefi.h>
#include <Protocol/DiskIo.h>

EFI_STATUS
EFIAPI
DiskIoReadDisk (
  IN  EFI_DISK_IO_PROTOCOL  *This,
  IN  UINT32                MediaId,
  IN  UINT64                Offset,
  IN  UINTN                 BufferSize,
  OUT VOID                  *Buffer
  )
{
  DISK_IO_PRIVATE_DATA  *Private;
  
  Private = DISK_IO_PRIVATE_DATA_FROM_THIS (This);
}
```

The `Stop()` function from the `EFI_DRIVER_BINDING_PROTOCOL` uses the same
`CR()` based macro to retrieve the private context data structure. The only
difference is that the _This_ pointer is not passed into the `Stop()` function.
Instead, the `Stop()` function uses _ControllerHandle_ to retrieve one of the
produced protocols and then uses the `CR()` based macro with that protocol
interface pointer to retrieve the private context data structure.

The example below shows a code fragment from the Driver Binding Protocol
`Stop()` service of the `DiskIoDxe` driver in the `MdeModulePkg`. It uses the
`CR()` based macro called `DISK_IO_PRIVATE_DATA_FROM_THIS()` and
`EFI_DISK_IO_PROTOCOL` retrieved from _ControllerHandle_ using the UEFI Boot
Service `OpenProtocol()` to retrieve the `DISK_IO_PRIVATE_DATA` private context
data structure.

###### Example 121-Retrieving the disk I/O private context data structure in Stop()

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/DiskIo.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS
EFIAPI
DiskIoDriverBindingStop (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN UINTN                        NumberOfChildren,
  IN EFI_HANDLE                   *ChildHandleBuffer
  )
{
  EFI_STATUS            Status;
  EFI_DISK_IO_PROTOCOL  *DiskIo;
  DISK_IO_PRIVATE_DATA  *Private;
  
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
}
```
