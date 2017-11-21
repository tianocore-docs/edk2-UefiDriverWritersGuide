<!--- @file
  14.2.1 Device Drivers

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

### 14.2.1 Device Drivers

Device drivers that implement `GetHealthStatus()` must verify that
_ChildHandle_ is `NULL` and that _ControllerHandle_ represents a device
currently under the device driver's management. The Driver Health Protocol also
supports returning the combined health status for all controllers under a UEFI
Driver's is management. This request is made by passing in a _ControllerHandle_
value of `NULL`.

The following example shows the steps required to check these parameters. If
these checks pass, the health status is returned. In this specific example, the
driver opens the PCI I/O Protocol in its Driver Binding `Start()` function.
This is why `gEfiPciIoProtocolGuid` is used in the call to the EDK II Library
`UefiLib` function `EfiTestManagedDevice()` that checks to see if the UEFI
Drivers providing this `GetHealthStatus()` service is currently managing _ControllerHandle_. If the private context structure is required, typically the UEFI Boot Service `OpenProtocol()` is used to open one of the UEFI Driver produced protocols on _ControllerHandle_ and then uses a `CR()` based macro to retrieve a pointer to the private context structure.

###### Example 153-GetHealthStatus() for a Device Driver

```c
#include <Uefi.h>
#include <Protocol/DriverHealth.h>
#include <Protocol/PciIo.h>
#include <Library/BaseMemoryLib.h>
#include <Library/UefiLib.h>

EFI_STATUS
EFIAPI
AbcGetHealthStatus (
  IN  EFI_DRIVER_HEALTH_PROTOCOL     *This,
  IN  EFI_HANDLE                     ControllerHandle,  OPTIONAL
  IN  EFI_HANDLE                     ChildHandle,       OPTIONAL
  OUT EFI_DRIVER_HEALTH_STATUS       *HealthStatus,
  OUT EFI_DRIVER_HEALTH_HII_MESSAGE  **MessageList,     OPTIONAL
  OUT EFI_HII_HANDLE                 *FormHiiHandle     OPTIONAL
  )
{
  EFI_STATUS  Status;
  //
  // Check input parameters
  //
  if (HealthStatus == NULL) {
    return EFI_INVALID_PARAMETER;
  }
  
  if (ControllerHandle == NULL) {
    //
    // If all controllers managed by this UEFI Driver are healthly,
    // then assign HealthStatus to EfiDriverHealthStatusHealthy.
    // Otherwise, assign HealthStatus to EfiDriverHealthStatusFailed.
    //
    return EFI_SUCCESS;
  }
  
  //
  // ChildHandle must be NULL for a Device Driver
  //
  if (ChildHandle != NULL) {
    return EFI_UNSUPPORTED;
  }
  
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
  // Retrieve health status for ControllerHandle
  //
  
  return EFI_SUCCESS;
}
```
