<!--- @file
  19.3.1 Driver Binding Protocol Supported()

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

### 19.3.1 Driver Binding Protocol Supported()

USB device drivers must implement the `EFI_DRIVER_BINDING_PROTOCOL` that
contains the `Supported()`, `Start()`, and `Stop()` services. The `Supported()`
service checks the passed-in controller handle to determine whether this handle
represents a USB device that the driver knows how to manage.

The following is the most common method for doing the check:
* Check if this handle has `EFI_USB_IO_PROTOCOL` installed. If not, this handle
  is not a USB device on the current USB bus.
* Get the USB interface descriptor back from the USB device. Check whether the
  values of this device's _InterfaceClass_, _InterfaceSubClass_, and
  _InterfaceProtocol_ are identical to the corresponding values this driver can manage.

If the handle passes the above two checks, the USB device driver can manage the device
that the controller handle represents and the `Supported()` service returns
`EFI_SUCCESS`. Otherwise, the `Supported()` service returns `EFI_UNSUPPORTED`.
In addition, this checking process must not disturb the current state of the USB
device because the USB device may be managed by another USB device driver.

The example below shows an implementation of the Driver Binding Protocol
`Supported()` service for a USB keyboard driver. It opens the USB I/O Protocol
with an attribute of `EFI_OPEN_PROTOCOL_BY_DRIVER`. It then uses the
`UsbGetInterfaceDescriptor()` service of the USB I/O Protocol and evaluates the
class, subclass, and protocol fields of the interface descriptor to see if the
description is for a USB keyboard.

###### Example 206-Supported() for a USB device driver

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/UsbIo.h>
#include <Library/UefiBootServicesTableLib.h>

#define CLASS_HID 3
#define SUBCLASS_BOOT 1
#define PROTOCOL_KEYBOARD 1

EFI_STATUS
EFIAPI
AbcSupported (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN EFI_DEVICE_PATH_PROTOCOL     *RemainingDevicePath   OPTIONAL
  )
{
  EFI_STATUS                      Status;
  EFI_USB_IO_PROTOCOL             *UsbIo;
  EFI_USB_INTERFACE_DESCRIPTOR    InterfaceDescriptor;
  
  //
  // Open the USB I/O Protocol on ControllerHandle
  //
  Status = gBS->OpenProtocol (
                  ControllerHandle,
                  &gEfiUsbIoProtocolGuid,
                  (VOID **)&UsbIo,
                  This->DriverBindingHandle,
                  ControllerHandle,
                  EFI_OPEN_PROTOCOL_BY_DRIVER
                  );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // Get the USB Interface Descriptor
  //
  Status = UsbIo->UsbGetInterfaceDescriptor (
                  UsbIo,
                  &InterfaceDescriptor
                  );
  if (EFI_ERROR (Status)) {
    goto Done;
  }
  
  //
  // Check to see if the interface descriptor is supported by this driver
  //
  if (InterfaceDescriptor.InterfaceClass != CLASS_HID || InterfaceDescriptor.InterfaceSubClass != SUBCLASS_BOOT ||
      InterfaceDescriptor.InterfaceProtocol != PROTOCOL_KEYBOARD ) {
    Status = EFI_UNSUPPORTED;
  }
  
Done:
  //
  // Close the PCI I/O Protocol
  //
  gBS->CloseProtocol (
         ControllerHandle,
         &gEfiUsbIoProtocolGuid,
         This->DriverBindingHandle,
         ControllerHandle
         );
 
  return Status;
}
```

Because the `Supported()` service is invoked many times, the USB bus driver in
EDK II makes certain optimizations. The USB bus driver caches the interface
descriptors, eliminating the need to read them from the USB device every time a
USB device driver's `Supported()` service is invoked.
