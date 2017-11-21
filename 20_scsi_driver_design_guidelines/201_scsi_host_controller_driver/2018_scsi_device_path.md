<!--- @file
  20.1.8 SCSI Device Path

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

### 20.1.8 SCSI Device Path

The SCSI host controller driver described in this document support a SCSI
channel that is generated or emulated by multiple architectures, such as
SCSI-I, SCSI-II, SCSI-III, ATAPI, Fibre Channel, iSCSI, and other future
channel types. This section describes four example device paths, including
SCSI, ATAPI, and Fibre Channel device paths.

#### 20.1.8.1 SCSI Device Path Example

The table below shows an example device path for a SCSI host controller that
supports a single SCSI channel and is located at PCI device number 0x07 and PCI
function 0x00 The PCI SCSI host controller is directly attached to a PCI root
bridge.

This sample device path consists of an ACPI device path node, a PCI device path node, and a device path end structure. The _HID and _UID must match the ACPI table description of the PCI root bridge. The following is the shorthand notation for this device path: ACPI(PNP0A03,0)/PCI(7|0).

<div style="page-break-after: always;"></div>

###### Table 32-SCSI device path examples

| **Byte offset** | **Byte length** | **Data**  | **Description**                                                           |
| -------- | -------- | ------- | ----------------------------------------------------------------------------------------- |
| 0x00     | 0x01     | 0x02    | Generic Device Path Header - Type ACPI Device Path                                        |
| 0x01     | 0x01     | 0x01    | Sub type - ACPI Device Path                                                               |
| 0x02     | 0x02     | 0x0C    | Length - 0x0C bytes                                                                       |
| 0x04     | 0x04     | 0x41D0, | _HID PNP0A03 - 0x41D0 represents a compressed string 'PNP' and is in the low-order bytes. |
|          |          | 0x0A03  |                                                                                           |
| 0x08     | 0x04     | 0x0000  | _UID                                                                                      |
| 0x0C     | 0x01     | 0x01    | Generic Device Path Header - Type Hardware Device Path                                    |
| 0x0D     | 0x01     | 0x01    | Sub type - PCI                                                                            |
| 0x0E     | 0x02     | 0x06    | Length - 0x06 bytes                                                                       |
| 0x10     | 0x01     | 0x07    | PCI Function                                                                              |
| 0x11     | 0x01     | 0x00    | PCI Device                                                                                |
| 0x12     | 0x01     | 0xFF    | Generic Device Path Header - Type End of Hardware Device Path                             |
| 0x13     | 0x01     | 0xFF    | Sub type - End of Entire Device Path                                                      |
| 0x14     | 0x02     | 0x04    | Length - 0x04 bytes                                                                       |

#### 20.1.8.2 Multiple SCSI channels on a multifunction PCI controller

A SCSI host controller with multiple SCSI channels on a multi-function PCI
controller only changes the PCI portion of the device path for each SCII
channel. In this example, SCSI channel 0 is accessed through PCI function #0,
and SCSI channel 1 is accessed through PCI function #1 The following are the
device paths for these SCSI channels:
* `ACPI(PNP0A03,1)/PCI(7|0)` - Access to channel 0
* `ACPI(PNP0A03,1)/PCI(7|1)` Access to channel 1

#### 20.1.8.3 Multiple SCSI channels on a single function PCI controller

If there is a SCSI PCI controller with multiple SCSI channels connected to a
single-function PCI device, the device paths must differentiate the SCSI
channels. In this example, SCSI channel 0 is accessed through Controller #0
below PCI function #0, and SCSI channel 1 is accessed through Controller #1
below PCI function #1. The following are the device paths for these SCSI
channels:
* `ACPI(PNP0A03,1)/PCI(7|0)/Controller(0)` - Access to channel 0
* `ACPI(PNP0A03,1)/PCI(7|0)/Controller(1)` - Access to channel 1

### 20.1.9 Using Extended SCSI Pass Thru Protocol

If a SCSI driver supports both blocking and non-blocking I/O modes, any client
of the SCSI driver can use them to perform SCSI I/O.

The following example demonstrates how to use Extended SCSI Pass Thru Protocol
to perform blocking and non-blocking I/O.

###### Example 218-Blocking and non-blocking modes

```c
#include <Uefi.h>
#include <Protocol/ScsiPassThruExt.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS
EFIAPI
ScsiPassThruTests (
   EFI_EXT_SCSI_PASS_THRU_PROTOCOL  *ScsiPassThru,
   UINT8                            *Target,
   UINT64                           Lun
  )
{
  EFI_STATUS                                  Status;
  EFI_EXT_SCSI_PASS_THRU_SCSI_REQUEST_PACKET  Packet;
  EFI_EVENT 
  Event;
  //
  // Fill in Packet for the requested test operation
  //
  
  //
  // Blocking I/O
  //
  Status = ScsiPassThru->PassThru (
                           ScsiPassThru,
                           Target,
                           Lun,
                           &Packet,
                           NULL
                           );
  
  //
  // Non Blocking I/O
  //
  Status = gBS->CreateEvent (
                  EVT_NOTIFY_SIGNAL,
                  TPL_CALLBACK,
                  NULL,
                  NULL,
                  &Event
                  );
				  
  Status = ScsiPassThru->PassThru (
                           ScsiPassThru,
                           Target,
                           Lun,
                           &Packet,
                           &Event
                           );
						   
  do {
    Status = gBS->CheckEvent (Event);
  } while (EFI_ERROR (Status));
  
  return Status;
}
```
