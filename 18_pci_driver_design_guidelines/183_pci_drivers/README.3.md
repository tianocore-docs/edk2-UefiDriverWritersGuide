<!--- @file
  18.3 PCI drivers

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

## 18.3 PCI drivers

PCI drivers use the services of the `EFI_PCI_IO_PROTOCOL` to produce one or
more protocols providing I/O abstractions for a PCI controller. PCI drivers
follow the UEFI driver model, so they may be any of the following:

* Device drivers

* Bus drivers

* Hybrid drivers

PCI drivers for graphics controllers are typically device drivers that consume
the `EFI_PCI_IO_PROTOCOL` and produce the `EFI_GRAPHICS_OUTPUT_PROTOCOL`. The
PCI drivers for USB host controllers are typically device drivers that consume
the `EFI_PCI_IO_PROTOCOL` and produce the `EFI_USB_HOST_CONTROLLER_PROTOCOL`.

The PCI drivers for disk controllers are typically bus drivers or hybrid
drivers that consume the `EFI_PCI_IO_PROTOCOL` and `EFI_DEVICE_PATH_PROTOCOL`
and produce child handles with the `EFI_DEVICE_PATH_PROTOCOL` and
`EFI_BLOCK_IO_PROTOCOL`.

PCI drivers for disk controllers using the SCSI command set typically produce
the `EFI_EXT_SCSI_PASS_THRU_PROTOCOL` for each SCSI channel the disk controller
produces.

_Chapter 20_ covers details on SCSI drivers.
