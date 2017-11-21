<!--- @file
  21.1 ATA Host Controller Driver

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

## 21.1 ATA Host Controller Driver

An ATA host controller driver manages a ATA host controller and installs ATA
Pass Thru Protocol and the Extended SCSI Pass Thru Protocol. See the ATA Pass
Thru Protocol section of the _UEFI Specification_ for details about
`EFI_ATA_PASS_THRU_PROTOCOL` and `EFI_EXT_SCSI_PASS_THRU_PROTOCOL`. Guidelines
for the Extended SCSI Pass Thru Protocol are covered in _Chapter 20_. The rest
of this section focuses on the ATA Pass Thru Protocol.

An ATA host controller driver is a device driver that follows the UEFI driver
model. Typically, ATA host controller drivers are chip-specific because of the
requirement to initialize and manage the currently bound ATA host controller.
Because there may be multiple ATA host adapters in a platform that may be
managed by a single ATA host controller driver, it is recommended that the ATA
host controller driver be designed to be re-entrant and allocate a different
private context data structure for each ATA host controller.

An ATA host controller driver performs the following:
* Install the ATA Pass Thru Protocol onto the controller handle for the ATA
  host controller.
* Install Extended SCSI Pass Thru Protocol onto the controller handle for the
  SCSI host controller.

The platform firmware typically provides the ATA Bus Driver that completes the
ATA driver stack by performing the following actions:
* Use the services of the ATA Pass Thru Protocol to scan for ATA targets
  connected to the ATA host controller and create child handles.
* Install Device Path Protocol to each child handle.
* Install Block I/O Protocol on each child handle.
* Install Block I/O 2 Protocol on each child handle.
* If the hard drive supports the SPC-4 or ATA8-ACS command set, then install
  the Storage Security Command Protocol the child handle.
