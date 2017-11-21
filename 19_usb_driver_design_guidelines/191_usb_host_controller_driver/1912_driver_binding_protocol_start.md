<!--- @file
  19.1.2 Driver Binding Protocol Start()

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

### 19.1.2 Driver Binding Protocol Start()

The `Start()` service of the Driver Binding Protocol for the USB host
controller driver also opens the PCI I/O Protocol with an attribute of
`EFI_OPEN_PROTOCOL_BY_DRIVER`. This is followed by an initialization of the USB
host controller hardware and an installation of a `EFI_USB2_HC_PROTOCOL`
instance into the Handle Database.

#### 19.1.2.1 Support for legacy devices

Some USB host controllers provide legacy support to be compatible with legacy
devices. Under this mode, the USB input device, including mouse and keyboard,
act as if they are behind an 8042 keyboard controller. A UEFI implementation
uses the native USB support rather than the legacy support.

As a result, the USB legacy support must be disabled in the `Start()` service
of the USB host controller driver, before enabling the USB host controller.
This step is required because the legacy support conflicts with the native USB
support provided in UEFI USB driver stack. The example below shows how to turn
off USB legacy support for a UHCI 1.1 Host Controllers.

###### Example 205-Disable USB Legacy Support

```c
///
/// USB legacy Support
///
#define USB_EMULATION 0xc0

EFI_STATUS
EFIAPI
TurnOffUSBLegacySupport (
  IN EFI_PCI_IO_PROTOCOL                     *PciIo
  )
{
  EFI_STATUS                                 Status;
  UINT16                                     Command;
  
  //
  // Disable USB Legacy Support by writing 0x0000 to the USB_EMULATION
  // register in the PCI Configuration of the PCI Controller
  //
  Command = 0;
  Status = PciIo->Pci.Write (
                        PciIo,               // This
                        EfiPciIoWidthUint16, // Width
                        USB_EMULATION,       // Offset
                        1,                   // Count
                        &Command             // Buffer
                        );
  return Status;
}
```
