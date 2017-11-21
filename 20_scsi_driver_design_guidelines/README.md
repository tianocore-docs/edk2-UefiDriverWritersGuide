<!--- @file
  20 SCSI Driver Design Guidelines

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

# 20 SCSI Driver Design Guidelines

There are several categories of SCSI drivers that cooperate to provide the SCSI
driver stack in a platform. Table 31 lists these SCSI drivers.

###### Table 31-Classes of SCSI drivers

| **Class of driver**         | **Description**                                                                        |
| --------------------------- | -------------------------------------------------------------------------------------- |
| SCSI host controller driver | Consumes PCI I/O Protocol on the SCSI host controller handle and produces the Ext SCSI Pass Thru Protocol. If a driver is required to be compatible with the EFI 1.10 Specification, then the SCSI Pass Thru Protocol must be produced. |
| SCSI bus driver             | Consumes the Ext SCSI Pass Thru Protocol and produces a child handle for SCSI targets on the SCSI bus. Installs the Device Path Protocol and SCSI I/O Protocol onto each child handle.                                                  |
| SCSI device driver          | Consumes the SCSI I/O Protocol and produces an I/O abstraction that provides services for the console devices and boot devices that are required to boot an EFI-conformant operating system.                                            |

This chapter shows how to write UEFI Drivers for SCSI host controllers and UEFI
Drivers for SCSI devices. SCSI drivers must follow all of the general design
guidelines described in _Chapter 4_ of this guide. In addition, any SCSI host
controllers that are PCI controllers must also follow the PCI-specific design
guidelines described in _Chapter 18_. This chapter covers the guidelines that
apply specifically to the management of SCSI host controllers, SCSI channels,
and SCSI devices. SCSI drivers, especially those for RAID controllers, may
include HII functionality for SCSI subsystem configuration settings. HII
functionality is described in _Chapter 12_ of this guide.

The _EFI 1.10 Specification_ defines the SCSI Pass Thru Protocol. UEFI Drivers
for SCSI host controllers that are required to work properly on platforms that
conform to the _EFI 1.10 Specification_ are required to produce the SCSI Pass
Thru Protocol and also produce the Block I/O protocol for physical and logical
drives that the SCSI host controller manages. This implies that a UEFI Driver
for the SCSI host controller in an EFI 1.10 platform is required to perform all
the functions of the SCSI driver stack described in the _table above_. The
_UEFI 2.0 Specification_ and above require the platform firmware to provide the
SCSI bus driver and SCSI device driver for mass storage devices, so the
implementation of a UEFI Driver for a SCSI host controller is simpler if the
UEFI Driver is only required to function properly on platforms that conform to
the _UEFI 2.0 Specification_ and above.
