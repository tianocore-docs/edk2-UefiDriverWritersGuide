<!--- @file
  21 ATA Driver Design Guidelines

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

# 21 ATA Driver Design Guidelines

There are several categories of ATA drivers that cooperate to provide the ATA
driver stack in a platform. The following table lists these ATA drivers.

###### Table 33-Classes of ATA drivers

| **Class of driver**        | **Description**                                                                                |
| -------------------------- | ---------------------------------------------------------------------------------------------- |
| ATA host controller driver | Consumes PCI I/O Protocol on the ATA host controller handle and produces the ATA Pass Thru Protocol used to access hard drives and the Ext SCSI Pass Thru Protocol used to access CD-ROM/DVD-ROM drives.     |
| ATA bus driver             | Consumes the ATA Pass Thru Protocol and creates child handles for ATA targets with the Device Path Protocol, Block I/O Protocol, Block I/O 2 Protocol, and optionally the Storage Security Command Protocol. |

This chapter shows how to write UEFI Drivers for ATA host controllers. ATA
drivers must follow all of the general design guidelines described in _Chapter
4_ of this guide. In addition, any ATA host controllers that are PCI
controllers must also follow the PCIspecific design guidelines described in
_Chapter 18_. This section covers the guidelines that apply specifically to the
management of ATA host controllers. ATA drivers, especially those for RAID
controllers, may include HII functionality for ATA subsystem configuration
settings. HII functionality is described in _Chapter 12_ of this guide.
