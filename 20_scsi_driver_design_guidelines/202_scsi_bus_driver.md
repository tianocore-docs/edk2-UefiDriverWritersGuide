<!--- @file
  20.2 SCSI Bus Driver

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

## 20.2 SCSI Bus Driver

EDK II contains a generic SCSI bus driver. This driver uses the services of
`EFI_EXT_SCSI_PASS_THRU_PROTOCOL` to enumerate SCSI devices and produce child
handles with `EFI_DEVICE_PATH_PROTOCOL` and `EFI_SCSI_IO_PROTOCOL`. The
implementation of the SCSI Bus Driver is found in the `MdeModulePkg` in the directory
`MdeModulePkg/Bus/Scsi/ScsiBusDxe`.

If UEFI-based system firmware is ported to a new platform, most of the
SCSI-related changes occur in the implementation of the SCSI host controller
driver. If new types of SCSI devices are introduced that are required to
provide a console or provide a UEFI boot capability, then the implementation of
new SCSI Device Drivers are also required. The SCSI bus driver is designed to
be a generic, platform-agnostic driver. As a result, customizing the SCSI bus
driver is `strongly discouraged`. The detailed design and implementation of the
SCSI bus driver is not covered in this guide.
