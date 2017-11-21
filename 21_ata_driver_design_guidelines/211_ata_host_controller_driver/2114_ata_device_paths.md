<!--- @file
  21.1.4 ATA Device Paths

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

### 21.1.4 ATA Device Paths

The table below shows an example device path for a ATA host controller that
supports a single SCSI channel and is located at PCI device number 0x07 and PCI
function 0x00 The PCI SCSI host controller is directly attached to a PCI root
bridge.

This sample device path consists of an ACPI device path node, a PCI device path node, and a device path end structure. The _HID and _UID must match the ACPI table description of the PCI root bridge. The following is the shorthand notation for this device path: `ACPI(PNP0A03,0)/PCI(7|0)`.

###### Table 34-SATA device path examples

| **Byte Offset** | **Byte Length** | **Data** | **Description**                                                            |
| -------- | -------- | ------- | ----------------------------------------------------------------------------------------- |
| 0x00     | 0x01     | 0x02    | Generic Device Path Header - Type ACPI Device Path                                        |
| 0x01     | 0x01     | 0x01    | Sub type - ACPI Device Path                                                               |
| 0x02     | 0x02     | 0x0C    | Length - 0x0C bytes                                                                       |
| 0x04     | 0x04     | 0x41D0, 0x0A03 | _HID PNP0A03 - 0x41D0 represents a compressed string 'PNP' and is in the low-order bytes. |
| 0x08     | 0x04     | 0x0000  | _UID                                                                                      |
| 0x0C     | 0x01     | 0x01    | Generic Device Path Header - Type Hardware Device Path                                    |
| 0x0D     | 0x01     | 0x01    | Sub type - PCI                                                                            |
| 0x0E     | 0x02     | 0x06    | Length - 0x06 bytes                                                                       |
| 0x10     | 0x01     | 0x07    | PCI Function                                                                              |
| 0x11     | 0x01     | 0x00    | PCI Device                                                                                |
| 0x12     | 0x01     | 0xFF    | Generic Device Path Header - Type End of Hardware Device Path                             |
| 0x13     | 0x01     | 0xFF    | Sub type - End of Entire Device Path                                                      |
| 0x14     | 0x02     | 0x04    | Length - 0x04 bytes                                                                       |
