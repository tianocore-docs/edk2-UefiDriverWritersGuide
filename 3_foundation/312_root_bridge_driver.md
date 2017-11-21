<!--- @file
  3.12 Root Bridge Driver

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

## 3.12 Root Bridge Driver

A root bridge driver does not produce any instances of the

`EFI_DRIVER_BINDING_PROTOCOL`. It is responsible for initializing and
immediately creating physical controller handles for the root bridge
controllers or root devices in a platform. The driver must install the Device
Path Protocol onto a physical controller handle because the root bridge
controllers or root devices represent physical devices. An example root bridge
driver, `PcAtChipsetPkg/PciHostBridgeDxe`, is shown in the EDK II.

This driver also installs the PCI Root Bridge I/O Protocol―the protocol
abstraction for a PCI Bus. This protocol is used by a bus driver for the PCI
Bus to enumerate the PCI controllers attached to the PCI root bridge.

A driver for a root device may produce a protocol that is more directly usable
as a console or boot device. For example, a Serial I/O Protocol for a serial
device that is not attached to an industry standard bus type supported by the
_UEFI Specification_, or a Block I/O Protocol for a block-oriented media device
that is not attached to an industry standard bus type supported by the _UEFI
Specification._
