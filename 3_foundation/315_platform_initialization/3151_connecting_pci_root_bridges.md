<!--- @file
  3.15.1 Connecting PCI Root Bridges

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

### 3.15.1 Connecting PCI Root Bridges

During UEFI-conformant firmware initialization by the platform, the system
typically uses the service `LoadImage()` to load a root bridge driver for the
root device. One common example is a PCI root bridge driver.

Like all drivers, as it loads, UEFI firmware creates a handle in the handle
database and attaches an instance of the `EFI_LOADED_IMAGE_PROTOCOL` with the
unique image information for the PCI root bridge driver. Because this driver is
the system root driver, it does not follow the UEFI Driver Model. Instead, it
immediately uses its knowledge about the platform architecture to create
handles for each PCI root bridge

As viewed using the `dh` UEFI Shell command below, a portion of the handle
database shows a single PCI root bridge. Some platforms, such as data center
servers, will have more than one PCI root bridge.

A PCI root bridge driver installs the `EFI_PCI_ROOT_BRIDGE_IO_PROTOCOL` and an
`EFI_DEVICE_PATH_PROTOCOL` onto a new handle. By not installing the Driver
Binding

Protocol, the PCI root bridge prevents itself from being disconnected or
reconnected later on. For example, the handle database as viewed with the `dh`
UEFI Shell command might look like the following after the PCI root bridge
driver is loaded and executed.

This example shows an image handle that is a single controller handle with a
PCI Root Bridge I/O Protocol and the Device Path Protocol.

```
...
B: Image(PcatPciRootBridge)
C: PciRootBridgeIo DevPath (Acpi(HWP0002,0,PNP0A03))
...
```

**********
**Note:** _PNP0A03 may appear in either _HID or _CID of the PCI root bridge
device path node. This example is one where it is not in _HID._
**********

OS loaders usually require access to the boot devices to complete an OS boot
operation. Boot devices must have a Device Path Protocol that represents the
unique name of the boot device. The Device Path Protocol for a boot device
attached to a PCI Bus would start with a single ACPI node `Acpi(HID, UID)` or
`Acpi(HID, UID, CID)`. This node also points the OS to the place in the ACPI
name space where the ACPI description of the PCI root bridge is stored. The
`EFI_PCI_ROOT_BRIDGE_IO_PROTOCOL` provides PCI functions that are used by the
PCI bus driver that is described in next section.
