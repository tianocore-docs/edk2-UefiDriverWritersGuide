<!--- @file
  13 UEFI Driver Diagnostics

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

# 13 UEFI Driver Diagnostics

The Driver Diagnostics Protocols are optional features that allow UEFI Drivers
following the UEFI Driver Model to provide diagnostics for the devices under
UEFI Driver management. Use of these protocols depends on the UEFI Driver Model
concepts so Service Drivers, Root Bridge Drivers, and Initializing Drivers
never produce the Driver Diagnostics Protocols.

The Driver Binding Protocol `Start()` function may perform some quick checks of
a device's status, but checks taking extended time to execute should be
provided in a Driver Diagnostic Protocol implementation. Doing so improves the
overall platform boot performance by deferring extensive diagnostics to a
separate protocol not required to execute on every boot.

The Driver Diagnostics Protocol and the Driver Diagnostics 2 Protocol are very
similar. The only difference lies in the type of language code used to specify
the language for diagnostic result messages. The Driver Diagnostic Protocol
uses ISO 639-2 language codes (i.e. `eng`, `fra`). The Driver Diagnostics 2
Protocol uses RFC 4646 language codes (i.e. `en`, `en-US`, `fr`). For
diagnostics provided to platforms conforming to the _EFI 1.10_Specification_, use the Driver Diagnostics Protocol. For diagnostics provided
to platforms conforming to the _UEFI 2.0 Specification_ or above, use the
Driver Diagnostics 2 Protocol. Since the only difference is the language code
for the diagnostic message results, UEFI Drivers required to provide
diagnostics typically produce both protocols so the two implementations can
share the same diagnostic algorithms and diagnostic result messages.

The Driver Diagnostics Protocols are installed onto handles in the driver entry
point of UEFI Drivers. _Chapter_ 7 provides details on the EDK II `UefiLib`
library that provides helper functions to initialize UEFI Drivers following the
UEFI Driver Model, including the installation of the Driver Diagnostics
Protocols.

The Driver Diagnostic Protocols may be invoked from a UEFI Boot Manager if a
platform provides those options to a user. A platform vendor can take advantage
of Driver Diagnostic Protocol implementations for devices to improve overall
system diagnostics for the user. These protocols may also be invoked through a
UEFI Application that performs diagnostics.

Use the `drvdiag` command to test the functionality of Driver Diagnostic
Protocol implementation and to diagnose issues on platforms that either build
the UEFI Shell in or provide the ability to boot the UEFI Shell from a boot
device. The `drvdiag` command provides the list of devices that support
diagnostic operations and the ability to run diagnostics on a specific device
and report the results.

If a controller is managed by more than one UEFI Driver, there may be multiple
instances of the Driver Diagnostics Protocols that apply to a single
controller. The consumers of the Driver Diagnostics Protocols have to decide
how the multiple drivers supporting diagnostics are presented to users so they
can select the desired diagnostic. For example, a PCI bus driver may produce
the Driver Diagnostics Protocol to verify the functionality of a specific PCI slot. The UEFI Driver for a SCSI adapter inserted into that same PCI slot may produce diagnostics for the SCSI host controller. Both sets of diagnostics may be useful to a user when testing the platform. The UEFI Shell `drvdiag` command does support this use case.

_Appendix B_ contains a table of example drivers from the EDK II along with the
features each implement. The EDK II provides example drivers with full
implementations of the Driver Diagnostics Protocols.

**********
**Note:** _The Driver Diagnostics Protocols are used rarely, and platform
vendors may or may not invoke the Driver Diagnostics Protocols._
**********
