<!--- @file
  19.1 USB Host Controller Driver

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

## 19.1 USB Host Controller Driver

The USB host controller driver depends on which USB host controller
specification the host controller is based. Currently, the major types of USB
host controllers are the following:
* Open Host Controller Interface (OHCI) (USB 1.0 and USB 1.1)
* Universal Host Controller Interface (UHCI) (USB 1.0 and USB 1.1)
* Enhanced Host Controller Interface (EHCI) (USB 2.0)
* Extended Host Controller Interface (XHCI) (USB 3.0)

The USB host controller driver is a device driver and follows the UEFI driver
model. It typically consumes the services of `EFI_PCI_IO_PROTOCOL` and produces
`EFI_USB2_HC_PROTOCOL`. The following section provides guidelines for
implementing the `EFI_DRIVER_BINDING_PROTOCOL` services and
`EFI_USB2_HC_PROTOCOL` services for the USB host controller driver. The EDK II
provides UEFI Drivers that implement the `EFI_USB_HC2_PROTOCOL` for UHCI, ECHI,
and XHCI in the MdeModulePkg in the following paths:
* UHCI - `MdeModulePkg/Bus/Pci/UhciDxe`
* EHCI - `MdeModulePkg/Bus/Pci/EhciDxe`
* XHCI - `MdeModulePkg/Bus/Pci/XhciDxe`
