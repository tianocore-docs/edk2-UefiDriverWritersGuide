<!--- @file
  3.9 UEFI device paths

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

## 3.9 UEFI device paths

UEFI defines a Device Path Protocol that is attached to device handles in the
handle database. The Device Path Protocol helps operating systems and their
loaders identify the hardware that a device handle represents.

The Device Path Protocol provides a unique name for each physical device in a
system. The collection of Device Path Protocols for the physical devices
managed by UEFI-based firmware is called a "name space."

Modern operating systems tend to use ACPI and industry standard buses to
produce a name space while the operating system is running. However, the ACPI
name space is difficult to parse, and it would greatly increase the size and
complexity of system firmware to carry an ACPI name space parser. Instead, UEFI
uses aspects of the ACPI name space that do not require an ACPI name space
parser. This compromise keeps the size and complexity of system firmware to a
minimum. It also provides a way for the operating system to create a mapping
from UEFI device paths to the operating system's name space.

A device path is a data structure that is composed of one or more device path
nodes. Every device path node contains a standard header that includes the
node's type, subtype, and length. This standard header allows a parser of a
device path to hop from one node to the next without having to understand every
type of node that may be present in the system.

The following two examples show the declaration of the PCI device path node
which combined the generic UEFI Device Path Header with the PCI-device-specific
fields _Function_ and _Device_.

###### Example 3-Device Path Header

```c
/**
  This protocol can be used on any device handle to obtain generic path/location information concerning the physical device or logical device. If the handle does not logically map to a physical device, the handle may not necessarily support the device path protocol. The device path describes the location of the device the handle is for. The size of the Device Path can be determined from the structures that make up the Device Path.
**/

typedef struct {
  UINT8 Type;       ///< 0x01 Hardware Device Path.
                    ///< 0x02 ACPI Device Path.
                    ///< 0x03 Messaging Device Path.
                    ///< 0x04 Media Device Path.
                    ///< 0x05 BIOS Boot Specification Device Path.
                    ///< 0x7F End of Hardware Device Path.
					
  UINT8 SubType;    ///< Varies by Type
                    ///< 0xFF End Entire Device Path, or
                    ///< 0x01 End This Instance of a Device Path and start a new
                    ///< Device Path.
					
  UINT8 Length[2];  ///< Specific Device Path data. Type and Sub-Type define
                    ///< type of data. Size of data is included in Length.
					
} EFI_DEVICE_PATH_PROTOCOL;
```

###### Example 4-PCI Device Path

```c
///
/// PCI Device Path.
///
typedef struct {
  EFI_DEVICE_PATH_PROTOCOL   Header;
  ///
  /// PCI Function Number.
  ///
  UINT8                      Function;
  ///
  /// PCI Device Number.
  ///
  UINT8                      Device;
} PCI_DEVICE_PATH;
```

Device paths are designed to be position-independent by not using pointer
values for any field. This independence allows device paths to be easily moved
from one location to another and stored in nonvolatile storage.

A device path is terminated by a special device path node called an end device
path node. See _Example 2_ in this section.

The following table lists the types of device path nodes that are defined in
the Device Path Protocol chapter of the _UEFI Specification_.

<div style="page-break-after: always;"></div>

###### Table 9-Types of device path nodes defined in UEFI Specification

| **Type of device path notes**                  | ** Description**                                                                        |
| ---------------------------------------------- | --------------------------------------------------------------------------------------- |
| Hardware device path node                      | Used to describe devices on industry-standard buses that are directly accessible through processor memory or processor I/O cycles. These devices include memory-mapped devices and devices on PCI buses and PC card buses.          |
| ACPI device path node                          | Used to describe devices whose enumeration is not described in an industry-standard fashion. This type of device path is used to describe devices such as PCI root bridges and ISA devices. These device path nodes contain HID, CID, and UID fields that must match the HID, CID, and UID values that are present in the platform's ACPI tables.                                                                                                              |
| Messaging device path node                     | Used to describe devices on industry-standard buses that are not directly accessible through processor memory or processor I/O cycles. These devices are accessed by the processor through one or more hardware bridge devices that translate one industrystandard bus type to another industry-standard bus type. This type of device path is used to describe devices such as SCSI, Fibre Channel, 1394, USB, I2O, InfiniBand(R), UARTs, and network agents. |
| Media device path node                         | Hard disk, CD-ROM, and file paths in a file system that supports multiple directory levels.      |
| BIOS Boot Specification (BBS) device path node | Used to describe a device that has a type that follows the BIOS Boot Specification_,_ such as floppy drives, hard disks, CD-ROMs, PCMCIA devices, USB devices, network devices, and bootstrap entry vector (BEV) devices. These device path nodes are used only in a platform that supports BIOS INT services.          |
| End device path node                           | Used to terminate a device path.         |

Each of the device path node types also supports a vendor-defined node that is
the extensibility mechanism for device paths. As new devices, bus types, and
technologies are introduced into platforms, new device path nodes types may
have to be created. The vendor-defined nodes use a GUID to distinguish new
device path nodes.

Careful design is required when choosing the data fields used in the definition
of a new device path node. As long as a device is not physically moved from one
location in a platform to another location, the device path must not change
across platform boots or if there are system configuration changes in other
parts of the platform. For example, the PCI device path node only contains a
_Device_ and a _Function_ field. It does not contain a _Bus_ field, because the
addition of a device with a PCI-to-PCI bridge may modify the bus numbers of
other devices in the platform.

Instead, the device path for a PCI device is described with one or more PCI
device path nodes that describe the path from the PCI root bridge, through zero
or more PCI-toPCI bridges, and finally the target PCI device.

The UEFI Shell is able to display a device path on a console as a string. The
conversion of device path nodes to printable strings is defined in the EFI
Device Path Display Format Overview section of the _UEFI Specification_. This
optional feature allows developers to view device paths in a readable form
using the UEFI shell. The UEFI Shell also provides a method to perform a hex
dump of a device path.

The example below shows some example device paths. These device paths show
standard and extended ACPI device path nodes being used for a PCI root bridge
and an ISA floppy controller. PCI device path nodes are used for PCI-to-PCI
bridges, PCI video controllers, PCI IDE controllers, and PCI-to-LPC bridges.
Finally, IDE messaging device path nodes are used to describe an IDE hard disk,
and media device path nodes are used to describe a partition on an IDE hard
disk.

###### Example 5-Device Path Examples

```c
//
// PCI Root Bridge #0 using an Extended ACPI Device Path
//
Acpi(HWP0002,PNP0A03,0)

//
// PCI Root Bridge #1 using an Extended ACPI Device Path
//
Acpi(HWP0002,PNP0A03,1)

//
// PCI Root Bridge #0 using a standard ACPI Device Path
//
Acpi(PNP0A03,0)

//
// PCI-to-PCI bridge device directly attached to PCI Root Bridge #0
//
Acpi(PNP0A03,0)/Pci(1E|0)

//
// A video adapter installed in a slot on the other side of a PCI-to-PCI bridge
// that is attached to PCI Root Bridge #0.
//
Acpi(PNP0A03,0)/Pci(1E|0)/Pci(0|0)

//
// A PCI-to-LPC bridge device attached to PCI Root Bridge #0
//
Acpi(PNP0A03,0)/Pci(1F|0)

//
// A 1.44 MB floppy disk controller attached to a PCI-to-LPC bridge device
// attached to PCI Root Bridge #0
//
Acpi(PNP0A03,0)/Pci(1F|0)/Acpi(PNP0604,0)

//
// A PCI IDE controller attached to PCI Root Bridge #0
//
Acpi(PNP0A03,0)/Pci(1F|1)

//
// An IDE hard disk attached to a PCI IDE controller attached to
// PCI Root Bridge #0
//
Acpi(PNP0A03,0)/Pci(1F|1)/Ata(Secondary,Master)

//
// Partition #1 of an IDE hard disk attached to a PCI IDE controller attached to
// PCI Root Bridge #0
//
Acpi(PNP0A03,0)/Pci(1F|1)/Ata(Secondary,Master)/HD(Part1,Sig00000000)
```
