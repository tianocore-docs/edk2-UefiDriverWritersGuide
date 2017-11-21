<!--- @file
  18.3.1 Supported()

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

### 18.3.1 Supported()

A PCI driver must implement the `EFI_DRIVER_BINDING_PROTOCOL` containing the
`Supported()`, `Start()`, and `Stop()` services. The `Supported()` service
evaluates the _ControllerHandle_ passed in to see if the _ControllerHandle_ represents a PCI
device the PCI driver can manage.

The most common method of implementing the test is for the PCI driver to
retrieve the PCI configuration header from the PCI controller and evaluate the device ID,
vendor ID, and, possibly, the class code fields of the PCI configuration
header. If these fields match the values the PCI driver knows how to manage,
`Supported()` returns

`EFI_SUCCESS`. Otherwise, the `Supported()` service returns `EFI_UNSUPPORTED`.
The PCI driver must be careful not to disturb the state of the PCI controller
because a different PCI driver may be managing the PCI controller.

**********
**Caution:** Do not allow functions to "touch" or change the state of any
hardware device in the `Supported()` function of the Driver Binding Protocol.
Doing so can significantly degrade the driver's performance and/or cause the
device, the driver, and/or other drivers to lose sync and behave badly and
unpredictably.
**********
**TIP:** When modifying PCI device registers, be careful with the bits in the
PCI device configuration space. Perform a read, then modify the desired bits,
then do a write. Do not perform only a write operation to the bits, since that
can reset other bits in the register.
**********

The following example shows an example of the Driver Binding Protocol
`Supported()` service for the ABC PCI driver managing a PCI controller with a
vendor ID of 0x8086 and a device ID of 0xFFFE.

First, it attempts to open the PCI I/O Protocol `EFI_OPEN_PROTOCOL_BY_DRIVER`
with `OpenProtocol()`. If the PCI I/O Protocol cannot be opened, the PCI driver
does not support the controller specified by _ControllerHandle_. If the PCI I/O
Protocol is opened, the services of the PCI I/O Protocol are used to read the
vendor ID and device ID from the PCI configuration header. Always closed the
PCI I/O Protocol with `CloseProtocol().` `EFI_SUCCESS` is returned if the
vendor ID and device ID match.

###### Example 169-Supported() Reading partial PCI Configuration Header

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h> #include <Protocol/PciIo.h>
#include <IndustryStandard/Pci.h>
#include <Library/UefiBootServicesTableLib.h>

#define ABC_VENDOR_ID 0x8086
#define ABC_DEVICE_ID 0xFFFE

EFI_STATUS
EFIAPI
AbcSupported (
  IN EFI_DRIVER_BINDING_PROTOCOL               *This,
  IN EFI_HANDLE                                ControllerHandle,
  IN EFI_DEVICE_PATH_PROTOCOL                  *RemainingDevicePath   OPTIONAL
  )
{
  EFI_STATUS                                   Status;
  EFI_PCI_IO_PROTOCOL                          *PciIo;
  UINT16                                       VendorId;
  UINT16                                       DeviceId;
  
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
  // Read 16-bit Vendor ID from the PCI configuration header at offset 0x00
  //
  Status = PciIo->Pci.Read (
                        PciIo,                 // This
                        EfiPciIoWidthUint16,   // Width
                        PCI_VENDOR_ID_OFFSET,  // Offset 
                        sizeof (VendorId),     // Count 
                        &VendorId              // Buffer
                        );
  if (EFI_ERROR (Status)) {
    goto Done;
  }
  
  //
  // Read 16-bit Device ID from the PCI configuration header at offset 0x02
  //
  Status = PciIo->Pci.Read (
                        PciIo,                 // This
                        EfiPciIoWidthUint16,   // Width
                        PCI_DEVICE_ID_OFFSET,  // Offset 
                        sizeof (DeviceId),     // Count  
                        &DeviceId              // Buffer
                        );
  if (EFI_ERROR (Status)) {
    goto Done;
  }
  
  //
  // Evaluate Vendor ID and Device ID
  //
  Status = EFI_SUCCESS;
  if (VendorId != ABC_VENDOR_ID || DeviceId != ABC_DEVICE_ID) {
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

The previous example performs two 16-bit reads from the PCI configuration
header.

The code would be smaller if the entire PCI configuration header were read at
once. However, this would increase the execution time because the `Supported()`
service reads the entire PCI configuration header for every _ControllerHandle_
passed in.

The `Supported()` service is intended to be a small, quick check. If a more
extensive evaluation of the PCI configuration header is required, it may make
sense to read the entire PCI configuration header at once. The example below
shows the same example as above, but differs in that it reads the entire PCI
configuration header in a single call to the PCI I/O Protocol reading, 32-bits
at a time.

###### Example 170-Supported() Reading entire PCI Configuration Header

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h> #include <Protocol/PciIo.h>
#include <IndustryStandard/Pci.h>
#include <Library/UefiBootServicesTableLib.h>

#define ABC_VENDOR_ID 0x8086
#define ABC_DEVICE_ID 0xFFFE

EFI_STATUS
EFIAPI
AbcSupported (
  IN EFI_DRIVER_BINDING_PROTOCOL                  *This,
  IN EFI_HANDLE                                   ControllerHandle,
  IN EFI_DEVICE_PATH_PROTOCOL                     *RemainingDevicePath   OPTIONAL
  )
{
  EFI_STATUS                                      Status;
  EFI_PCI_IO_PROTOCOL                             *PciIo;
  PCI_TYPE00                                      Pci;

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
  // Read the entire PCI configuration header using 32-bit reads
  //
  Status = PciIo->Pci.Read (
                        PciIo,                    // This
                        EfiPciIoWidthUint32,      // Width
                        0,                        // Offset sizeof 
                        (Pci) / sizeof (UINT32),  // Count 
                        &Pci                      // Buffer
                        );
  if (EFI_ERROR (Status)) {
    goto Done;
  }
  
  //
  // Evaluate Vendor ID and Device ID
  //
  Status = EFI_SUCCESS;
  if (Pci.Hdr.VendorId != ABC_VENDOR_ID || Pci.Hdr.DeviceId != ABC_DEVICE_ID ) {
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
