<!--- @file
  3.10.1 Device driver

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

### 3.10.1 Device driver

The `Start()` service of a device driver installs protocol(s) directly onto the
controller handle that was passed into the `Start()` service. The protocol(s)
installed by the device driver use the I/O services that are provided by the
bus I/O protocol that is installed on the controller handle. For example, a
device driver for a USB device uses the service of the USB I/O Protocol, and a
device driver for a PCI controller uses the services of the PCI I/O Protocol.
In other words, the PCI I/O Protocol is consumed by a driver for a PCI option
ROM card. This process is called "consuming the bus I/O abstraction."

The following are the main objectives of the device driver:

* Initialize the controller.

* Install an I/O protocol on the device that can be used directly or indirectly
  by UEFI-conformant system firmware to boot an operating system.

It does not make sense to write device drivers for devices that cannot be used
to boot a platform. The following table provides the list of standard I/O
protocols that the _UEFI Specification_ defines for different classes of
devices. If multiple protocols are listed, that does not necessarily mean that
all the protocols must be produced. Please see later sections of the guide and
the _UEFI Specification_ for details on which protocols are required and which
are optional.

<div style="page-break-after: always;"></div>

###### Table 11-I/O protocols produced in the Start() function for different device classes

| **Class of device**                                 | **Protocol(s) created in the Start section of the driver**         |
| --------------------------------------------------- | ------------------------------------------------------------------ |
| Block Oriented Device                               | `EFI_BLOCK_IO2_PROTOCOL`                                           |
|                                                     | `EFI_BLOCK_IO_PROTOCOL`                                            |
|                                                     | `EFI_STORAGE_SECURITY_COMMAND_PROTOCOL`                            |
| File System                                         | `EFI_SIMPLE_FILE_SYSTEM_PROTOCOL`                                  |
| Non block oriented or file system based boot device | `EFI_LOAD_FILE_PROTOCOL`                                           |
| LAN                                                 | Universal Network Driver Interface (UNDI)                          |
|                                                     | `EFI_NETWORK_INTERFACE_IDENTIFIER_PROTOCOL`                        |
|                                                     | `EFI_SIMPLE_NETWORK_PROTOCOL`                                      |
|                                                     | `EFI_MANAGED_NETWORK_PROTOCOL`                                     |
|                                                     | `EFI_VLAN_CONFIG_PROTOCOL`                                         |
|                                                     | `EFI_BIS_PROTOCOL`                                                 |
| Graphics Display                                    | `EFI_GRAPHICS_OUTPUT_PROTOCOL`                                     |
|                                                     | `EFI_EDID_DISCOVERED_PROTOCOL`                                     |
|                                                     | `EFI_EDID_ACTIVE_PROTOCOL`                                         |
| Text Console                                        | `EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL`                                  |
| Character based I/O device                          | `EFI_SERIAL_IO_PROTOCOL`                                           |
| Keyboard                                            | `EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL`                                |
|                                                     | `EFI_SIMPLE_TEXT_INPUT_PROTOCOL`                                   |
| Mouse                                               | `EFI_SIMPLE_POINTER_PROTOCOL`                                      |
| Tablet                                              | `EFI_ABSOLUTE_POINTER_PROTOCOL`                                    |
| USB Host Controller                                 | `EFI_USB2_HC_PROTOCOL EFI_USB_HC_PROOCOL`                          |
| SCSI Host Controller                                | `EFI_EXT_SCSI_PASS_TRU_PROTOCOL`                                   |
|                                                     | `EFI_SCSI_PASS_THRU_PROTOCOL`                                      |
| SATA Controller                                     | `EFI_ATA_PASS_THRU_PROTOCOL`                                       |
| Credential Provider for User Authentication         | `EFI_USER_CREDENTIAL2_PROTOCOL`                                    |

The fundamental definition of a UEFI device driver is that it does not create
any child handles. This difference distinguishes a device driver from a bus
driver.

The definition of a device driver can be confusing because it is often
necessary to write a driver that creates child handles. This necessity makes
the driver a bus driver by definition, even though the driver may not be
managing a hardware bus in the classical sense (such as a PCI, SCSI, USB, or
Fibre Channel bus).

Even though a device driver does not create child handles, the device managed
by the device driver could still become a "parent." The protocol(s) produced by
a device driver on a controller handle may be consumed by a bus driver that
produces child handles. In this case, the controller handle that is managed by
a device driver is a parent controller. This scenario happens quite often.

For example, the `EFI_USB2_HC_PROTOCOL` is produced by a device driver called
the _USB host controller driver._ The protocol is consumed by the USB bus
driver. The USB bus driver creates child handles that contain the
`USB_IO_PROTOCOL`. The USB host controller driver that produced the
`EFI_USB2_HC_PROTOCOL` has no knowledge of the child handles that are produced
by the USB bus driver.
