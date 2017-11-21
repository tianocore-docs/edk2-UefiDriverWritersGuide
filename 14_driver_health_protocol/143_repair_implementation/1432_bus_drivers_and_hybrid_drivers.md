<!--- @file
  14.3.2 Bus Drivers and Hybrid Drivers

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

### 14.3.2 Bus Drivers and Hybrid Drivers

Bus drivers and hybrid drivers that implement the Driver Health Protocol must
verify that _ControllerHandle_ and _ChildHandle_ represent a device that the
driver is currently managing. The example below shows the steps required to
check these parameters and also retrieve the private context data structure. If
these checks pass, the health status is returned. In this specific example, the
driver opens the PCI I/O Protocol in its Driver Binding `Start()` function.

This is why `gEfiPciIoProtocolGuid` is used in the call to the EDK II Library
`UefiLib` function `EfiTestManagedDevice()` that checks to see if the UEFI
Drivers providing this `Repair()` service are currently managing
_ControllerHandle_. If the private context structure is required, typically the
UEFI Boot Service `OpenProtocol()` is used to open one of the UEFI Driver
produced protocols on _ControllerHandle_ and then uses a `CR()` based macro to
retrieve a pointer to the private context structure.

If diagnostics are being run on _ChildHandle_, a produced protocol on
_ChildHandle_ can be opened. This example also calls _ProgressNotification_
from 10% to 100% at 10% increments for the bus controller and from 1% to 100%,
at 1% increments, for the child controller.

**********
**Note:** _If ChildHandle is_ **NULL**, _a request is made to run diagnostics
on the bus controller. If_ _ChildHandle is not_ **NULL**_, a request is made to
run diagnostics on a UEFI Driver managed child controller._
**********

###### Example 156-Repair() for a Bus Driver or Hybrid Driver

```c
#include <Uefi.h>
#include <Protocol/DriverHealth.h>
#include <Protocol/PciIo.h>
#include <Library/BaseMemoryLib.h>
#include <Library/UefiLib.h>

EFI_STATUS EFIAPI
AbcRepair (
  IN EFI_DRIVER_HEALTH_PROTOCOL                *This,
  IN EFI_HANDLE                                ControllerHandle,
  IN EFI_HANDLE                                ChildHandle,           OPTIONAL
  IN EFI_DRIVER_HEALTH_REPAIR_PROGRESS_NOTIFY  ProgressNotification   OPTIONAL
  )
{
  EFI_STATUS  Status;
  UINTN       Index;
  
  //
  // Make sure this driver is currently managing ControllerHandle
  //
  Status = EfiTestManagedDevice (
             ControllerHandle,
             gAbcDriverBinding.DriverBindingHandle,
             &gEfiPciIoProtocolGuid
             );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // If ChildHandle is not NULL, then make sure this driver produced ChildHandle
  //
  if (ChildHandle != NULL) {
    Status = EfiTestChildHandle (
               ControllerHandle,
               ChildHandle,
               &gEfiPciIoProtocolGuid
               );
    if (EFI_ERROR (Status)) {
      return Status;
    }
  }
  
  if (ChildHandle == NULL) {
    //
    // Repair ControllerHandle
    //
    for (Index = 0;
         Index < 10; Index++) {
      //
      // Perform 10% of the work required to repair ControllerHandle
      //
      if (ProgressNotification != NULL) {
        ProgressNotification (Index, 10);
      }
    }
  } else {
    //
    // Repair ChildHandle
    //
    for (Index = 0;
         Index < 100; Index++) {
      //
      // Perform 1% of the work required to repair ChildHandle
      //
      if (ProgressNotification != NULL) {
        ProgressNotification (Index, 100);
      }
    }
  }
  
  return EFI_SUCCESS;
}
```
