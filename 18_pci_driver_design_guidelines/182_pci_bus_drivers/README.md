<!--- @file
  18.2 PCI Bus Drivers

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

## 18.2 PCI Bus Drivers

EDK II contains a generic PCI bus driver. It uses the services of the
`EFI_PCI_ROOT_BRIDGE_IO_PROTOCOL` to enumerate PCI devices and produce a child
handle with an `EFI_DEVICE_PATH_PROTOCOL` and an `EFI_PCI_IO_PROTOCOL`. The
source code to this driver is in the EDK II package called `MdeModulePkg` in the directory
`MdeModulePkg/Bus/Pci/PciBusDxe`.

This bus type can support producing one child handle at a time by parsing the
_RemainingDevicePath_ in its `Supported()` and `Start()` services. However,
producing one child handle at a time for a PCI bus generally does not make
sense. This is because the PCI bus driver needs to enumerate and assign
resources to all of the PCI devices before even a single child handle can be
produced.

It does not take much extra time to produce the child handles for all the
enumerated PCI devices. Because of this, it is recommended that the PCI bus
driver produce all of the PCI devices on the first call to `Start()`.

If a UEFI based system firmware is ported to a new platform, most of the
PCI-related changes occur in the implementation of the Root Bridge Driver
producing the `EFI_PCI_ROOT_BRIDGE_IO_PROTOCOL` instances.

**********
**TIP:** PCI Bus Driver customizations are **strongly discouraged** because the
PCI Bus Driver is designed to be conformant with the _PCI Specification_.
Instead, focus platform specific customizations on the Root Bridge Driver that
produced **EFI_PCI_ROOT_BRIDGE_IO_PROTOCOL** and its PCI Device Drivers.
**********
