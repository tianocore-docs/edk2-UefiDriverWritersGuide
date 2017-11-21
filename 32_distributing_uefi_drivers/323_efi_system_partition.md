<!--- @file
  32.3 EFI System Partition

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

## 32.3 EFI System Partition

A device manufacturer that produces UEFI Drivers for their devices may choose
to have their UEFI Drivers installed onto an EFI System Partition on a target
platform. This method may be selected for UEFI Drivers that cannot be
distributed using the two methods described above. It may also be a convenient
method for UEFI Driver updates to be distributed and installed onto target
platforms. See the _UEFI Specification_ for details on EFI System Partitions
and [_http://www.uefi.org_](http://www.uefi.org/) for details on how device
manufacturers can reserve a subdirectory name for use on EFI System Partitions.

**********
**Note:** _There is no requirement for UEFI conformant platform firmware
implementations to load UEFI Drivers from EFI System Partitions. The capability
is defined by the UEFI Specification but there is no requirement that all
platforms implement this capability._
**********

Since this method depends on being able to access the EFI System Partition, the
UEFI Driver that is to be installed on the EFI System Partition must not be
required to access the EFI System Partition itself, either directly or
indirectly. For example, a UEFI Driver for a SCSI Host Controller cannot be
installed on an EFI System Partition of a mass storage device attached to that
same SCSI Host Controller. However, if the SCSI Host Controller is a PCI add-in
card with a UEFI Driver in a PCI Option ROM or the UEFI Driver for the SCSI
Host Controller is integrated in the platform firmware, it may be possible to
install an update to the UEFI Driver for the SCSI Host Controller in an EFI
System Partition on one of the mass storage devices attached to that SCSI Host
Controller as long as the UEFI Driver in the PCI Option ROM or the platform
firmware is functional enough to load the updated UEFI Driver from the EFI
System Partition.

There are a few steps that must be performed in order for a UEFI Driver to be
installed onto an EFI System Partition and for that UEFI Driver to be
automatically loaded and executed each time the target platform is booted.

1. The UEFI Driver must be copied onto a mass storage device that contains an
   EFI System Partition. This may require a custom UEFI Application to perform
   this transfer, or utilities such as 1 the UEFI Shell and UEFI Shell scripts
   may be used to install a UEFI Driver into a device specific directory.

2. Update the `Driver####` and `DriverOrder` UEFI variables so the UEFI Driver
   installed on the EFI System Partition is automatically loaded and executed
   on every boot. These variables can be updated from a custom UEFI
   Application, or OEM setup screens if this option is exposed.

**********
**Tip:** Use the UEFI Shell **drivers** command to view the set of UEFI Drivers
that have been loaded and executed to verify that a UEFI Driver that has been
installed and configured to load from EFI System Partition has actually been
loaded and executed by the platform firmware.
**********
