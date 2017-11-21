<!--- @file
  20.1.4 Implementing driver binding protocol

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

### 20.1.4 Implementing driver binding protocol

A SCSI host controller driver follows the UEFI driver model, so the image entry
point of a SCSI host controller driver installs the Driver Binding Protocol
instance on the image handle. All three of the services in the Driver Binding
Protocol-`Supported()`, `Start()`, and `Stop()`-must be implemented by a SCSI
host controller driver.

#### 20.1.4.1 Supported()

The `Supported()` function tests to see if a given controller handle is SCSI
adapter the driver knows how to manage. In this function, a SCSI host
controller driver checks to see if the `EFI_DEVICE_PATH_PROTOCOL` and
`EFI_PCI_IO_PROTOCOL` are present to ensure the handle that is passed in
represents a PCI device. In addition, a SCSI host controller driver checks the
_ClassCode_, _VendorId_, and _DeviceId_ from the device's PCI configuration
header to see if it is a conformant SCSI adapter that can be managed by the
SCSI host controller driver.

#### 20.1.4.2 Start()

The `Start()` function tells the SCSI host controller driver to start managing
the SCSI host controller. In this function, a single channel SCSI host
controller driver uses chipspecific knowledge to perform the following tasks:
* Initialize the SCSI host controller.
* Enable the PCI device.
* Allocate resources.
* Construct data structures for the driver to use.
* Install the Extended SCSI Pass Thru Protocol instance on the same handle that
  has the PCI I/O Protocol.

If the SCSI adapter is a multi-channel adapter, then the driver should also do
the following:
* Enumerate the SCSI channels that are supported by the SCSI host controller.
* Create child handles for each physical SCSI channel.
* Append the device path for each channel handle.
* Install the Device Path Protocol and Extended SCSI Pass Thru Protocol on
  every newly created channel handle.

#### 20.1.4.3 Stop()

The `Stop()` function performs the opposite operations as `Start()`. Generally
speaking, a SCSI driver is required to do the following:
* Disable the SCSI adapter.
* Release all resources that were allocated for this driver.
* Close the protocol instances that were opened in the `Start()`function.
* Uninstall the protocol interfaces that were attached on the host controller
  handle.

In general, if it is possible to design a SCSI host controller driver to create
one child at a time, it should do so to support the rapid boot capability in
the UEFI driver model.

Each of the channel child handles created in `Start()` must contain a Device
Path Protocol instance and a Extended SCSI Pass Thru abstraction layer.
