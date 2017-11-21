<!--- @file
  3.6.3 Protocols provided in addition to the _UEFI Specification_

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

### 3.6.3 Protocols provided in addition to the _UEFI Specification_

Not all protocols are defined in the _UEFI Specification_. For example, the EDK
II_,_ like other developer's kits, includes additional protocols that are not
part of the _UEFI Specification_. These additional protocols are necessary to
provide all of the functionality in a particular implementation but they are
not defined in the current _UEFI Specification_ because they do not present an
external interface-a requirement to support booting of an OS or writing of a
UEFI driver.

The creation of new protocols is how UEFI-based systems can be extended over
time as new devices, buses, and technologies are introduced.

The following are a few examples of protocols in the EDK II that are not part
of the _UEFI Specification:_

* Print 2 Protocol

* `MdeModulePkg/Include/Protocol/Print2.h`

* Deferred Procedure Call Protocol

* `MdeModulePkg/Include/Protocol/Dpc.h`

* VGA Mini Port Protocol

* `IntelFrameworkModulePkg/Include/Protocol/VgaMiniPort.h`

UEFI Drivers and UEFI OS Loaders should not depend on these types of protocols
because they are not guaranteed to be present in every UEFI-conformant firmware
implementation. UEFI Drivers and UEFI OS Loaders should depend only on
protocols defined in the current _UEFI Specification_ and protocols required by
platform design guides (i.e. _DIG64_). The extensible nature of UEFI allows
each platform to design and add its own special protocols. Use these protocols
to expand the capabilities of UEFI and provide access to proprietary devices
and interfaces congruent with the rest of the UEFI architecture.
