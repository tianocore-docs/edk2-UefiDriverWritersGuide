<!--- @file
  8.3 Allocating private context data structures

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

## 8.3 Allocating private context data structures

Private context data structures are allocated in the `Start()` function of the
Driver Binding Protocol. The service that is typically used to allocate the
private context data structures is the UEFI Boot Service `AllocatePool()`. The
following example shows the generic template for allocating and zeroing a
private context data structure in the `Start()` function of the Driver Binding
Protocol. In this example, the UEFI Boot Service `SetMem()` is used to fill the
allocated buffer with zeros. This code example shows only a fragment from the
`Start()` function. _Chapter 9_ of this guide covers the services that are
produced by the Driver Binding Protocol in more detail. The code examples that
follow show how the implementation of `Start()` can be simplified by using the
EDK II library `MemoryAllocationLib`.

###### Example 115-Allocation of a private context data structure

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/DevicePath.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS
EFIAPI
<<DriverName>>DriverBindingStart (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN EFI_DEVICE_PATH_PROTOCOL     *RemainingDevicePath   OPTIONAL
  )
{
  EFI_STATUS  Status;
  <<DRIVER_NAME>>_PRIVATE_DATA Private;
  
  //
  // Allocate the private context data structure
  //
  Status = gBS->AllocatePool (
                  EfiBootServicesData,
                  sizeof ( <<DRIVER_NAME>>_PRIVATE_DATA),
                  (VOID **)&Private
                  );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // Clear the contents of the allocated buffer
  //
  gBS->SetMem (Private,sizeof(<<DRIVER_NAME>>_PRIVATE_DATA),0);
}
```

The example below shows the same generic template for the `Start()` function
above except that it uses the EDK II library `MemoryAllocationLib` to allocate
and zero the private context data structure.

###### Example 116-Library allocation of private context data structure

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/DevicePath.h>
#include <Library/MemoryAllocationLib.h>

EFI_STATUS
EFIAPI
<<DriverName>>DriverBindingStart (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN EFI_DEVICE_PATH_PROTOCOL     *RemainingDevicePath   OPTIONAL
  )
{
  <<DRIVER_NAME>>_PRIVATE_DATA Private;
  
  //
  // Allocate and zero the private context data structure
  //
  Private = AllocateZeroPool (sizeof ( <<DRIVER_NAME>>_PRIVATE_DATA));
  if (Private == NULL) {
    return EFI_OUT_OF_RESOURCES;
  }
}
```

The following example shows a code fragment from the `DiskIoDxe` driver in the
`MdeModulePkg` that allocates and initializes the private context data
structure from a template structure. A template structure is an instance of the
private context structure with most of the fields pre-initialized. This style
produces UEFI Drivers that execute faster and produce smaller executables than
UEFI Drivers that initialize each field of the private context data structure
in the `Start()` function.

###### Example 117-Disk I/O allocation of private context data structure

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/DevicePath.h>
#include <Protocol/DiskIo.h>
#include <Library/MemoryAllocationLib.h>

//
// Template for DiskIo private data structure.
// The pointer to BlockIo protocol interface is assigned dynamically.
//
DISK_IO_PRIVATE_DATA gDiskIoPrivateDataTemplate = {
  DISK_IO_PRIVATE_DATA_SIGNATURE,
  {
    EFI_DISK_IO_PROTOCOL_REVISION,
    DiskIoReadDisk,
    DiskIoWriteDisk
  },
  NULL
};

EFI_STATUS
EFIAPI
DiskIoDriverBindingStart (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN EFI_DEVICE_PATH_PROTOCOL     *RemainingDevicePath   OPTIONAL
  )
{
  EFI_STATUS            Status;
  DISK_IO_PRIVATE_DATA  *Private;
  
  //
  // Initialize the Disk IO device instance.
  //
  Private = AllocateCopyPool (
              sizeof (DISK_IO_PRIVATE_DATA),
              &gDiskIoPrivateDataTemplate
              );
  if (Private == NULL) {
    Status = EFI_OUT_OF_RESOURCES;
    goto ErrorExit;
  }
}
```
