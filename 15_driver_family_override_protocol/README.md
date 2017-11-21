<!--- @file
  15 Driver Family Override Protocol

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

# 15 Driver Family Override Protocol

The Driver Family Override Protocol is an optional feature for UEFI Drivers
following the UEFI Driver Model. The Driver Family Override Protocol provides a method for a
UEFI Driver to opt-in to a higher priority rule for connecting drivers to
controllers in the EFI Boot Service `ConnectController()`. This rule is higher
priority than the Bus Specific Driver Override Protocol rule and lower priority
than the Platform Driver Override Rule.

The Driver Family Override Protocol is typically produced by UEFI Drivers
associated with a family of similar controllers when multiple versions of a
UEFI Driver for a family of similar controllers are present in a platform at
the same time and where the UEFI Driver writer requires that the UEFI Driver
considered the highest version manage all controllers in that same family.

**PCI Use Case**: If a platform has 3 PCI SCSI adapters from the same
manufacturer, and the manufacturer requires the PCI SCSI adapter that has the
highest version UEFI Driver to manage all 3 PCI SCSI adapters, then the Driver
Family Override Protocol is required and it provides the version value used to
make the selection. If the Driver Family Override Protocol is not produced,
then the Bus Specific Driver Override Protocol for PCI selects the UEFI Driver
from the PCI Option ROM to the adapter to manage each adapter.
