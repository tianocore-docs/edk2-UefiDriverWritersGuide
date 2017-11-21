<!--- @file
  5.2.3 LocateDevicePath()

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

### 5.2.3 LocateDevicePath()

This service locates a device handle that supports a specific protocol and has
the closest matching device path. Although a rare requirement, it is useful
when a UEFI Driver needs to find an I/O abstraction for one of its parent
controllers.

Normally, a UEFI Driver uses the services on the _ControllerHandle_ that is
passed into the `Supported()` and `Start()` functions of the EFI driver's
`EFI_DRIVER_BINDING_PROTOCOL`. However, if a UEFI Driver does require the use of services from a parent
controller, `LocateDevicePath()` can be used to find the handle of a parent
controller.

For example, a PCI device driver normally uses the PCI I/O Protocol to manage a
PCI controller. Hypothetically, if a PCI device driver required the services of
the PCI Root Bridge I/O Protocol of which the PCI controller is a child, then
the `gBS->LocateDevicePath()` function can be used to find the parent handle that
supports the PCI Root Bridge I/O Protocol. Then the `gBS->OpenProtocol()`
service can be used to retrieve the PCI Root Bridge I/O Protocol interface from
that handle.

The code fragment below shows how a UEFI Driver for a PCI Controller can
retrieve the PCI Root Bridge I/O Protocol of which the PCI controller is a
child.

**********
**Caution:** This operation is provided only as an illustration and is not
recommended because a parent bus driver typically owns the parent I/O
abstractions. Directly using a parent I/O may cause unintended side effects.
**********

_Section 18.4.2_, _Example 175_, contains another example showing the
recommended method for a PCI driver to access the resources of other PCI
controllers on the same PCI adapter without using the PCI Root Bridge I/O
Protocol.

<div style="page-break-after: always;"></div>

###### Example 63-Locate Device Path

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS Status;
EFI_HANDLE ControllerHandle;
EFI_DEVICE_PATH_PROTOCOL *DevicePath;
EFI_HANDLE ParentHandle;
EFI_PCI_ROOT_BRIDGE_IO_PROTOCOL *PciRootBridgeIo;

//
// Retrieve the Device Path Protocol instance on ControllerHandle
//
Status = gBS->OpenProtocol (
                ControllerHandle,
                &gEfiDevicePathProtocolGuid,
                (VOID **)&DevicePath,
                gImageHandle,
                ControllerHandle,
                EFI_OPEN_PROTOCOL_GET_PROTOCOL
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Find a parent controller that supports the
// PCI Root Bridge I/O Protocol
//
Status = gBS->LocateDevicePath (
                &gEfiPciRootBridgeIoProtocolGuid,
                &DevicePath,
                &ParentHandle
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Get the PCI Root Bridge I/O Protocol instance on ParentHandle
//
Status = gBS->OpenProtocol (
                ParentHandle,
                &gEfiPciRootBridgeIoProtocolGuid,
                (VOID **)&PciRootBridgeIo,
                gImageHandle,
                ControllerHandle,
                EFI_OPEN_PROTOCOL_GET_PROTOCOL
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```
