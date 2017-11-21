<!--- @file
  21.1.1 Implementing Driver Binding Protocol

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

### 21.1.1 Implementing Driver Binding Protocol

An ATA host controller driver follows the UEFI driver model, so the image entry
point of a ATA host controller driver installs the Driver Binding Protocol
instance on the image handle. All three of the services in the Driver Binding
Protocol-`Supported()`, `Start()`, and `Stop()`-must be implemented by a ATA
host controller driver.

#### 21.1.1.1 Supported()

The `Supported()` function tests to see if a given controller handle is an ATA
controller the driver knows how to manage. In this function, an ATA host
controller driver checks to see if the `EFI_DEVICE_PATH_PROTOCOL` and
`EFI_PCI_IO_PROTOCOL` are present to ensure the handle that is passed in
represents a PCI device. In addition, an ATA host controller driver checks the
_ClassCode_, _VendorId_, and _DeviceId_ from the device's PCI configuration
header to see if it is a conformant ATA controller that can be managed by the
ATA host controller driver.

#### 21.1.1.2 Start()

The `Start()` function tells the ATA host controller driver to start managing
the ATA host controller. In this function, an ATA host controller driver uses
chip-specific knowledge to perform the following tasks:
* Initialize the ATA host controller.
* Enable the PCI device.
* Allocate resources.
* Construct data structures for the driver to use.
* Install the ATA Pass Thru Protocol instance on the same handle that has the
  PCI I/O Protocol.
* Install the Extended SCSI Pass Thru Protocol instance on the same handle that
  has the PCI I/O Protocol.

If the ATA host controller provides RAID capabilities, then the ATA host
controller driver can either choose to only expose access to the logical drives
following the algorithm above, or the ATA host controller driver can produce two instances of
the ATA Pass Thru Protocol. One for accessing the physical drives, and another
for accessing the logical drives. In this case, a child handle is created for
each ATA Pass Thru Protocol instance.

#### 21.1.1.3 Stop()

The `Stop()` function performs the opposite operations as `Start()`. Generally
speaking, an ATA host controller driver is required to do the following:
* Disable the ATA controller.
* Release all resources that were allocated for this driver.
* Close the protocol instances that were opened in the `Start()`function.
* Uninstall the protocol interfaces that were attached on the host controller
  handle.
