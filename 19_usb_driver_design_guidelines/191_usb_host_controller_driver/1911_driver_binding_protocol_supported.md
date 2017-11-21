<!--- @file
  19.1.1 Driver Binding Protocol Supported()

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

### 19.1.1 Driver Binding Protocol Supported()

The USB host controller driver must implement the `EFI_DRIVER_BINDING_PROTOCOL`
containing the `Supported()`, `Start()`, and `Stop()` services. The Driver
Binding Protocol is installed into the Handle Database in the drive entry point.

The `Supported()` service evaluates the _ControllerHandle_ that is passed in to
check if the _ControllerHandle_ represents a USB host controller that the USB
host controller driver knows how to manage. The typical method of implementing
this evaluation is for the USB host controller driver to retrieve the PCI
configuration header from this controller and check the Class Code field and
possibly other fields such as the Device ID and Vendor ID. If all these fields
match the values that the USB host controller driver knows how to manage, the
`Supported()` service returns `EFI_SUCCESS`. Otherwise, the `Supported()`
service returns `EFI_UNSUPPORTED`.

The following example shows an example of the `Supported()` service for the
USB host controller driver managing a PCI controller with Class code 0x30c.

First, it attempts to open the PCI I/O Protocol `EFI_OPEN_PROTOCOL_BY_DRIVER`.
If the PCI I/O Protocol cannot be opened, then the USB host controller driver
does not support the controller specified by _ControllerHandle_. If the PCI I/O
Protocol is opened, the services of the PCI I/O Protocol are used to read the
Class Code from the PCI configuration header. The PCI I/O Protocol is always
closed with `CloseProtocol()`, and `EFI_SUCCESS` is returned if the Class Code
fields match.

###### Example 204-Supported() service for USB host controller driver

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h> #include <Protocol/PciIo.h>
#include <IndustryStandard/Pci.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS
EFIAPI
AbcSupported (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN EFI_DEVICE_PATH_PROTOCOL     *RemainingDevicePath   OPTIONAL
  )
{
  EFI_STATUS           Status;
  EFI_PCI_IO_PROTOCOL  *PciIo;
  UINT8 PciClass[3];
  
  //
  // Open the PCI I/O Protocol on ControllerHandle
  //
  Status = gBS->OpenProtocol (
                  ControllerHandle,
                  &gEfiPciIoProtocolGuid,
                  (VOID **)&PciIo,
                  This->DriverBindingHandle,
                  ControllerHandle,
                  EFI_OPEN_PROTOCOL_BY_DRIVER
                  );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // Read the 3 bytes of class code information from the PCI configuration header
  // at offset 0x09
  //
  Status = PciIo->Pci.Read (
                        PciIo, // This
                        EfiPciIoWidthUint8, // Width
                        PCI_CLASSCODE_OFFSET,
                        // Offset sizeof (PciClass),
                        // Count &PciClass
                        // Buffer
                        );
  if (EFI_ERROR (Status)) {
    goto Done;
  }
  
  //
  // Test whether the class code is for a USB UHCI controller
  //
  if ((PciClass[2] != PCI_CLASS_SERIAL) || (PciClass[1] != PCI_CLASS_SERIAL_USB) ||
      (PciClass[0] != PCI_IF_UHCI) ) {
    Status = EFI_UNSUPPORTED;
  }
  
Done:
  //
  // Close the PCI I/O Protocol
  //
  gBS->CloseProtocol (
         ControllerHandle,
         &gEfiPciIoProtocolGuid,
         This->DriverBindingHandle,
         ControllerHandle
         );

  return Status;
}
```
