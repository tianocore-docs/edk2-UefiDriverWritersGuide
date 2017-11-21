<!--- @file
  20.1 SCSI Host Controller Driver

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

## 20.1 SCSI Host Controller Driver

A SCSI host controller driver manages a SCSI host controller that contains one
or more SCSI channels. It creates handles for each SCSI channel and installs the
Extended SCSI Pass Thru Protocol and Device Path Protocol to each of the handle that the
driver creates. See the SCSI Driver Models and Bus Support chapter of the _UEFI
Specification_ for details about `EFI_EXT_SCSI_PASS_THRU_PROTOCOL`.

A SCSI host controller driver follows the UEFI driver model. Depending on the
adapter that it manages, a SCSI host controller driver can be categorized as
either a device driver or a hybrid driver. It creates child handles for each
SCSI channel (if there is more than 1) and it may also install protocols on its
own handle. Typically, SCSI host controller drivers are chip-specific because
of the requirement to initialize and manage the currently bound SCSI host
controller.

Because there may be multiple SCSI host adapters in a platform that may be
managed by a single SCSI host controller driver, it is recommended that the
SCSI host controller driver be designed to be re-entrant and allocate a
different private context data structure for each SCSI host controller.
