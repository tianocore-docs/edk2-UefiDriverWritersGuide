<!--- @file
  5.2.4 LoadImage() and StartImage()

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

### 5.2.4 LoadImage() and StartImage()

Use `LoadImage()`to load and relocate a UEFI Image into system memory, and
prepare it for execution. Use `StartImage()`to transfer control to a UEFI Image
that was previously loaded into system memory using `LoadImage()`. These
services are typically used by the UEFI Boot Manager when processing load
options for UEFI Drivers, UEFI Applications, or UEFI OS Loaders. UEFI drivers
do not typically need to load other UEFI Drivers and/or UEFI applications.

One exception is a bus driver for a bus type that provides a storage container
for UEFI Drivers and/or UEFI Applications. A PCI Option ROM is an example of a
container with those attributes. A PCI Bus Driver is required to discover any
PCI Option ROM containers present on PCI Adapters. If a PCI Option ROM contains
one or more UEFI Drivers that are compatible with the currently executing CPU,
then the PCI Bus Driver is required to load and start those UEFI Drivers using
the `LoadImage()` and `StartImage()` services. The EDK II PCI Bus Driver that
performs this operation can be found in `MdeModulePkg/Bus/Pci/PciBusDxe`.

Another exception is a UEFI Driver that needs to execute a UEFI Application for
the purposes of extended diagnostics or to augment driver configuration. There
are UEFI standard methods for a UEFI Driver to provide diagnostics and
configuration through the use of the `EFI_DRIVER_DIAGNOSTICS2_PROTOCOL` and
HII. If for some reason, a UEFI Driver requires diagnostics or configuration
capabilities that cannot be expressed using these standard methods, a UEFI
Driver could choose to execute a UEFI Application that provides those
capabilities. In the case of a PCI Adapter, UEFI Applications could be stored
in the PCI Option ROM container. The UEFI Driver would use the `LoadImage()`
and `StartImage()` services to load and execute those UEFI Applications from
that container.

The following code fragment in _Example 64_ shows an example of a UEFI Driver
for a PCI controller that uses the `LoadImage()` and `StartImage()` service to load
and execute a 32 KB UEFI Application that is stored 32 KB into the PCI Option ROM container
associated with the PCI controller. `PciControllerHandle` is the `EFI_HANDLE`
for the PCI Controller.

This example retrieves both the PCI I/O Protocol and the Device Path Protocol
associated with `PciControllerHandle`. The Device Path Protocol is used to
construct a proper device path for the UEFI Application stored in the PCI
option ROM. Helper functions from the EDK II library `DevicePathLib` are used
to fill in the contents of a new device path node for the UEFI Application
stored in the PCI Option ROM and to append that device path node to the device
path of the PCI controller. Use the PCI I/O Protocol to access the shadowed
copy of the PCI Option ROM contents through the `RomImage` field. The shadowed
copy of the PCI Option ROM was created when the PCI bus was enumerated and the
PCI I/O Protocols were produced.

**********
**Note:** _The use of a 32 KB offset and 32 KB length simplifies this example.
An addin adapter that stores UEFI Applications in a PCI Option ROM container
would likely define vendor specific descriptors to determine the offset and
size of one or more UEFI Applications._
**********

<div style="page-break-after: always;"></div>

###### Example 64-Load and Start a UEFI Application from a PCI Option ROM

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/DevicePathLib.h>
#include <Protocol/DevicePath.h>
#include <Protocol/PciIo.h>

EFI_STATUS Status;
EFI_HANDLE PciControllerHandle;
EFI_PCI_IO_PROTOCOL *PciIo;
EFI_DEVICE_PATH_PROTOCOL *PciDevicePath;
MEDIA_RELATIVE_OFFSET_RANGE_DEVICE_PATH OptionRomNode;
EFI_DEVICE_PATH_PROTOCOL *PciOptionRomDevicePath;
EFI_HANDLE NewImageHandle;

//
// Retrieve PCI I/O Protocol associated with PciControllerHandle
//
Status = gBS->OpenProtocol (
                PciControllerHandle,
                &gEfiPciIoProtocolGuid,
                (VOID **)&PciIo,
                gImageHandle,
                NULL,
                EFI_OPEN_PROTOCOL_GET_PROTOCOL
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Retrieve Device Path Protocol associated with PciControllerHandle
//
Status = gBS->OpenProtocol (
                PciControllerHandle,
                &gEfiDevicePathProtocolGuid,
                (VOID **)&PciDevicePath,
                gImageHandle,
                NULL,
                EFI_OPEN_PROTOCOL_GET_PROTOCOL
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Create Device Path Protocol to UEFI Application in PCI Option ROM
//
OptionRomNode.Header.Type = MEDIA_DEVICE_PATH;
OptionRomNode.Header.SubType = MEDIA_RELATIVE_OFFSET_RANGE_DP;
SetDevicePathNodeLength (&OptionRomNode.Header, sizeof (OptionRomNode));
OptionRomNode.StartingOffset = BASE_32KB;
OptionRomNode.EndingOffset = BASE_64KB - 1;
PciOptionRomDevicePath = AppendDevicePathNode (
                           PciDevicePath,
                           &OptionRomNode.Header
                           );
						   
//
// Load UEFI Image from PCI Option ROM container
//
Status = gBS->LoadImage (
                FALSE,
                gImageHandle,
                PciOptionRomDevicePath,
                (UINT8 *)(PciIo->RomImage) + SIZE_32KB,
                SIZE_32KB,
                &NewImageHandle
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Start UEFI Image from PCI Option ROM container
//
Status = gBS->StartImage (NewImageHandle, NULL, NULL);
if (EFI_ERROR (Status)) {
  return Status;
}
```
