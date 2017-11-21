<!--- @file
  19.3 USB Device Driver

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

## 19.3 USB Device Driver

USB device drivers use services provided by `EFI_USB_IO_PROTOCOL` to produce
one or more protocols that provide I/O abstractions of a USB device. USB device
drivers must follow the UEFI Driver Model. As mentioned above, the USB device
drivers do not manage hub devices because those hub devices are managed by the
USB bus driver. The EDK II provides a number of USB Device Drivers in the
`MdeModulePkg` for devices that are typically used to provide UEFI consoles and
UEFI boot devices. The EDK II `MdePkg` also provides a library called
`UefiUsbLib` that provides functions to simplify the implementations of USB
device drivers using the USB I/O Protocol. Some of the USB Device Driver
implementations provided in the EDK II are as follows:
* USB Keyboard: `MdeModulePkg/Bus/Usb/UsbKbDxe`
* USB Mouse: `MdeModulePkg/Bus/Usb/UsbMouseDxe`
* USB Mouse: `MdeModulePkg/Bus/Usb/UsbMouseAbsolutePointerDxe`
* USB Mass Storage: `MdeModulePkg/Bus/Usb/UsbMassStorageDxe`
