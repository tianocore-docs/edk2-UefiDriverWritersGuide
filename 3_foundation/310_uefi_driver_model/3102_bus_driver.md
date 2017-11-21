<!--- @file
  3.10.2 Bus driver

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

### 3.10.2 Bus driver

A bus driver is nearly identical to a device driver except that a bus driver
creates child handles. This capability leads to several added features and
responsibilities for a bus driver that are addressed in detail throughout this
document. For example, device drivers do not need to concern themselves with
searching the bus.

Just as with a device driver, the `Start()` function of a bus driver consumes
the parent bus I/O abstraction(s) and produces new I/O abstractions in the form
of protocols. For example, the PCI _bus driver_ consumes the services of the

`EFI_PCI_ROOT_BRIDGE_IO_PROTOCOL` and uses these services to scan a PCI bus for
PCI controllers. Each time a PCI controller is found, a child handle is created
and the

`EFI_PCI_IO_PROTOCOL` is installed on the child handle. The services of the
`EFI_PCI_IO_PROTOCOL` are implemented using the services of the
`EFI_PCI_ROOT_BRIDGE_IO_PROTOCOL`

As a second example, the USB bus driver uses the services of the
`EFI_USB2_HC_PROTOCOL` to discover and create child handles that support the
`EFI_USB_IO_PROTOCOL` for each USB device on the USB bus. The services of the
`EFI_USB_IO_PROTOCOL` are implemented using the services of the
`EFI_USB2_HC_PROTOCOL`.

The following are the main objectives of the bus driver:

* Initialize the bus controller.

* Determine how many children to create. For example, the PCI bus driver may
  discover and enumerate all PCI devices on the bus or only a single PCI device
  that is being used to boot. How a bus driver handles this step creates

a basic subdivision in the types of bus drivers. A bus driver can do one of the
following:

* Create handles for all child controllers on the first call to Start().

* Allow the handles for the child controllers to be created across multiple
  calls to Start().

* A bus driver that creates child handles across multiple Start() calls is very
  useful because it may reduce the platform boot time. It allows a few child
  handles, or even a single child handle, to be created across multiple calls
  to Start(). On buses that take a long time to enumerate their children (for
  example, SCSI and Fibre Channel), multiple calls to Start() can save a large
  amount of time when booting a platform.

* Allocate resources and create a child handle in the UEFI handle database for
  one or more child controllers.

* Install an I/O protocol on the child handle that abstracts the I/O operations
  that the controller supports (such as the PCI I/O Protocol or the USB I/O
  Protocol).

* If the child handle represents a physical device, then install a Device Path
  Protocol (see the _UEFI Specification_).

* Load drivers from option ROMs, if present. The PCI bus driver is currently
  the only bus driver that loads from option ROMs.

Some common examples of UEFI bus drivers include:

* `PCI Bus Driver`: Creates a child handle for PCI controllers, either directly
  attached to a PCI Root Bridge, or attached to a PCI Root Bridge through one
  or more PCI to PCI Bridges. The Device Path Protocol includes `Pci()` device
  path nodes.

* `USB Bus Driver`: Creates a child handle for USB devices, either directly
  attached to a USB Root Port, or attached to a USB Root Port through one or
  more USB Hubs. The Device Path Protocol includes `Usb()` device path nodes.

* `SCSI Bus Driver`: Creates a child handle for SCSI devices attached to a SCSI
  channel. The Device Path Protocol includes `Scsi()` device path nodes.

* `SATA Bus Driver`: Creates a child handle for SATA devices attached to a SATA
  ports. The Device Path Protocol includes `Sata()` device path nodes.

Because bus drivers are defined as drivers that produce child handles, there
are some other drivers that unexpectedly qualify as bus drivers:

* `Serial Driver:` Creates a child handle and extends the Device Path Protocol
  to include a `Uart()` messaging device path node.

* `LAN Driver:` Creates a child handle and extends the Device Path Protocol to
  include a `Mac()` address-messaging device path node.

* `Graphics Driver:` Creates a child handle for each physical video output and
  any logical video output that is a combination of two or more physical video
  outputs. Graphics drivers do not extend the Device Path Protocol.
