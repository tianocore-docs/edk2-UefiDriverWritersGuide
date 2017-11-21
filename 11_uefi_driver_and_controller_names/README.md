<!--- @file
  11 UEFI Driver and Controller Names

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

# 11 UEFI Driver and Controller Names

Both Component Name Protocols are optional features that allow UEFI Drivers
following the UEFI Driver Model to provide a localized Unicode name string for
the UEFI Driver and the devices the UEFI Driver manages. Use of these protocols
depends on the UEFI Driver Model concepts. Service Drivers, Root Bridge
Drivers, and Initializing Drivers never produce the Component Name Protocols.
Implementation of this optional feature is `recommended` for all UEFI Drivers
that follow the UEFI Driver Model.

**********
**Note:** _Human-readable names should be limited to about 40 Unicode
characters in length. This makes it easier for consumers of this protocol to
display these names on standard console devices._
**********

The Component Name Protocol and the Component Name 2 Protocol are very similar.
The only difference is the format of language code passed into the protocol
services to request the name of a UEFI Driver or the name of a device that a
UEFI Driver manages. The use of a language code allows the implementation of
the Component Name Protocols to provide names of drivers and devices in many
different languages.

The Component Name Protocol uses ISO 639-2 language codes (i.e. `eng`, `fra`).
The Component Name 2 Protocol uses RFC 4646 language codes (i.e. `en`, `en-US`,
`fr`). If names are provided for platforms conforming to the _EFI 1.10
Specification_, the Component Name s Protocol is required. If names are
provided for platforms that conforming to the _UEFI 2.0 Specification_ or
above, the Component Name 2 Protocol is required. Since the only difference is
the language code for the names, UEFI Drivers required to provide names
typically produce both protocols and the both use the same underlying functions
and Unicode name strings.

The Component Name Protocols are installed onto handles in the driver entry
point of a UEFI Driver. _Chapter 7_ describes details on the EDK II library
`UefiLib` that provides helper functions to initialize UEFI Drivers following
the UEFI Driver Model including installation of Component Name Protocols.

Component Name Protocols may be used by a UEFI Boot Manager to display human
readable names for drivers and devices in a specific language. A platform
vendor may also take advantage of Component Name Protocols from UEFI
Applications, such as system utilities or diagnostics, when human readable
names of UEFI drivers or devices are required.

The UEFI Shell provides several commands that use the Component Name Protocols.
For example, the `drivers` command displays the inventory of UEFI drivers in a
platform and uses the Component Name Protocols to display the name of a UEFI
Driver if the UEFI Driver produced the Component Name Protocols. Likewise, the
UEFI Shell command `devices` displays the inventory of devices in a platform
and uses the Component Name Protocols to display the name of the devices if a
UEFI Driver managing the device produced the Component Name Protocols.

If a controller is managed by more than one UEFI Driver, there may be multiple
instances of the Component Name Protocols that apply to a single controller.
The consumers of the Component Name Protocols have to decide how the multiple
drivers providing names are presented to the user. For example, a PCI bus
driver may produce a name for a PCI slot such as "PCI Slot #2," and the driver
for a SCSI adapter that is inserted into that same PCI slot may produce a name
like "XYZ SCSI Host Controller." Both names describe the same physical device
from each driver's perspective, and both names are useful depending on how they
are used.

_Appendix B_ contains a table of example drivers from the EDK II along with the
features that each implement. The EDK II provides example drivers with full
implementations of the Component Name Protocols.
