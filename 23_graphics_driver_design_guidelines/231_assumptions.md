<!--- @file
  23.1 Assumptions

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

## 23.1 Assumptions

The rest of this chapter assumes that the _Driver Checklist_ in _Chapter 2_ has
been followed and that the following items have already been identified:

* UEFI Driver Type

* Optional UEFI Driver features

* Supported CPU architectures

* Consumed protocols that are used to produce the graphics controller related
  protocols.

If the UEFI Driver is required to be compiled for EBC, then see _Chapter 18_
for PCI optimizations and _Chapter 29_ for EBC considerations. UEFI Drivers for
graphics controllers are typically more sensitive to the EBC virtual machine interpreter
overheads, so it is critical that the performance guidelines are followed for a
UEFI Driver for a graphics controller that is compiled for EBC to have good
performance.

UEFI Drivers for graphics controllers typically follow the UEFI driver model.
Some graphics controllers have a single output controller, and other may have
multiple output controllers. In both cases, a child handle must be created for
each output controller, which means UEFI Drivers for graphics controllers are
always either Bus Drivers or Hybrid Drivers. They are never Device Drivers.
UEFI Drivers for graphics controllers are chip-specific because of the
requirement to initialize and manage the graphics device.

UEFI drivers that manage graphics controllers typically follow the UEFI Driver
Model because the devices are typically on industry standard busses such as
PCI. However, it is possible to implement UEFI drivers for graphics controllers
that are not on industry standard busses. In these cases, a Root Bridge Driver
implementation that produces a handle for each output controller in the driver
entry point may be more appropriate than a UEFI Driver Model implementation.
