<!--- @file
  3.15.5 Console variables

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

### 3.15.5 Console variables

After loading these drivers in the handle database, the platform can connect
the console devices that the user has selected. The device paths for these
consoles are stored in the _ConIn_, _ConOut_, and _ErrOut_ global UEFI
variables (see the Boot Manager chapter of the _UEFI Specification_). For the
purpose of this example, the variables have the following device paths:

```
ErrOut = Acpi(HWP0002,0,PNP0A03)/Pci(1|1)/Uart(9600N81)/
VenMsg(Vt100+);Acpi(HWP0002,0,PNP0A03)/Pci(4|0)

ConOut = Acpi(HWP0002,0,PNP0A03)/Pci(1|1)/Uart(9600N81)/
VenMsg(Vt100+);Acpi(HWP0002,0,PNP0A03)/Pci(4|0)

ConIn = Acpi(HWP0002,0,PNP0A03)/Pci(1|1)/Uart(9600N81)/ 
VenMsg(Vt100+)
```

Note the following:

* The _ErrOut_ and _ConOut_ variables are multi-instance device paths separated
  by semicolon (;) indicating that the EFI output is mirrored on two different
  console devices. The mirroring is performed when the `ConSplitterDxe` driver
  is connected. In this example, the two devices are a serial terminal and a
  PCI video controller.

* The _ConIn_ variable contains a device path to a serial terminal.

* The _ErrOut_ variable is typically the same as the _ConOut_ variable, but
  could be redirected to different set of devices. It is important to check how
  this UEFI variable is configured when developing UEFI drivers because the
  debug messages from a UEFI Driver are typically directed to the console
  device(s) specified by _ErrOut_._ErrOut_ may not specify the same devices as
  _ConOut_

In this example, the two devices are a serial terminal and a PCI video
controller. The EDK II provides the `DebugLib` which is a library that provides
services such as `DEBUG()` and `ASSERT()` that are used generate debug messages.
