<!--- @file
  21.2 ATA Bus Driver

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

## 21.2 ATA Bus Driver

EDK II contains a generic ATA bus driver. This driver uses the services of
`EFI_ATA_PASS_THRU_PROTOCOL` to enumerate ATA devices and produce child handles
with `EFI_DEVICE_PATH_PROTOCOL`, `EFI_BLOCK_IO_PROTOCOL`,
`EFI_BLOCK_IO2_PROTOCOL`, and optionally the `EFI_STORAGE_SECURITY_COMMAND_PROTOCOL`.
The implementation of the ATA Bus Driver is found in the `MdeModulePkg` in the directory
`MdeModulePkg/Bus/Ata/AtaBusDxe`

If UEFI-based system firmware is ported to a new platform, most of the
ATA-related changes occur in the implementation of the ATA host controller
driver. The ATA bus driver is designed to be a generic, platform-agnostic
driver. As a result, customizing the ATA bus driver is `strongly discouraged`.
The detailed design and implementation of the ATA bus driver is not covered in
this guide.
