<!--- @file
  20.3.1 Driver Binding Protocol Supported()

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

### 20.3.1 Driver Binding Protocol Supported()

SCSI device drivers and must implement the `EFI_DRIVER_BINDING_PROTOCOL` that
contains the `Supported()`, `Start()`, and `Stop()` services. The `Supported()`
service checks the controller handle that has been passed in to see whether
this handle represents a SCSI device that this driver knows how to manage.

The following is the most common method for doing the check:
* Check if this handle has `EFI_SCSI_IO_PROTOCOL` installed. If not, this
  handle is not a SCSI device on the current SCSI channel.
* Retrieve the 8-bit SCSI device type to see if the type is one that this
  driver can manage.

If the above two checks are passed, it means that the SCSI device driver can
manage the device that the controller handle represents. The `Supported()`
service returns `EFI_SUCCESS`. Otherwise, the `Supported()` service returns
`EFI_UNSUPPORTED`. In addition, this check process must not disturb the current
state of the SCSI device, because a another SCSI device driver may be managing
this SCSI device.

The following example shows an implementation of the Driver Binding Protocol
`Supported()` service for a SCSI mass storage device. It opens the SCSI I/O
Protocol with an attribute of `EFI_OPEN_PROTOCOL_BY_DRIVER`. It then used the
`GetDeviceType()` service of the SCSI I/O Protocol and evaluates the type
information to see if it is a hard drive or a CD-ROM.

###### Example 219-Supported() for a SCSI device driver

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h> #include <Protocol/ScsiIo.h>
#include <IndustryStandard/Scsi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS
EFIAPI
AbcSupported (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN EFI_DEVICE_PATH_PROTOCOL     *RemainingDevicePath   OPTIONAL
  )
{
  EFI_STATUS            Status;
  EFI_SCSI_IO_PROTOCOL  *ScsiIo;
  UINT8                 DeviceType;
  
  //
  // Open the SCSI I/O Protocol on ControllerHandle
  //
  Status = gBS->OpenProtocol (
                  ControllerHandle,
                  &gEfiScsiIoProtocolGuid,
                  (VOID **)&ScsiIo,
                  This->DriverBindingHandle,
                  ControllerHandle,
                  EFI_OPEN_PROTOCOL_BY_DRIVER
                  );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // Get the SCSI Device Type
  //
  Status = ScsiIo->GetDeviceType (ScsiIo, &DeviceType);
  if (EFI_ERROR (Status)) {
    goto Done;
  }
  
  //
  // Check to see if the interface descriptor is supported by this driver
  //
  if ((DeviceType != EFI_SCSI_TYPE_DISK ) && (DeviceType != EFI_SCSI_TYPE_CDROM) ) {
    Status = EFI_UNSUPPORTED;
  }
  
Done:
  //
  // Close the SCSI I/O Protocol
  //
  gBS->CloseProtocol (
         ControllerHandle,
         &gEfiScsiIoProtocolGuid,
         This->DriverBindingHandle,
         ControllerHandle
         );
 
  return Status;
}
```
