<!--- @file
  12.7 Porting to UEFI HII functionality

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

## 12.7 Porting to UEFI HII functionality

HII allows the platform's existing browser to be used to display and manage
forms for user input. In doing so, HII functionality `replaces` or supplements
older protocols:

* Driver Configuration Protocol and Driver Configuration 2 Protocol:. If a UEFI
  Driver is required to only be compatible with the UEFI 2.1 Specification or
  higher, then `replace` the use of these protocols with HII functionality.

* Simple Text Input Protocol, Simple Text Output Protocol: UEFI Drivers, in
  general, are not allowed to use UEFI console protocols. The one exception is
  the Driver Configuration Protocol `SetOptions()` service. If a UEFI Driver is
  required to only be compatible with _UEFI 2.1 Specification_ or higher, the
  Driver Configuration Protocols are not required and the Simple Text Input
  Protocol and Simple Text Output Protocol should not be used.

* Convert strings used by Driver Configuration Protocol SetOptions() to a .uni
  file.

* Convert questions and other user interactions in Driver Configuration
  Protocol `SetOptions()` to a .vfr file. Only use HII callbacks if absolutely
  required.

* Convert Driver Configuration Protocol `ForceDefaults()` functionality into
  .vfr sources.

* Convert Driver Configuration Protocol `OptionsValid()` functionality into
  .vfr sources.
