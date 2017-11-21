<!--- @file
  6.1.3 Compatibility with Older EFI/UEFI Specifications

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

### 6.1.3 Compatibility with Older EFI/UEFI Specifications

The following lists features that a device driver can optionally implement to
provide compatibility with older versions of the _EFI_ and _UEFI
Specifications_.

* Install one or more instances of the `EFI_COMPONENT_NAME_PROTOCOL` in the
  driver's entry point.

  Implementing this feature is **strongly recommended** for drivers required to be
compatible with EFI 1.10 It allows a driver to provide human-readable names for
the name of the driver and the controllers that the driver is managing. The EDK
II libraries provide easy methods to produce both the Component Name Protocol
and the Component Name 2 Protocol with very little additional overhead.

* Installs one or more instances of the `EFI_DRIVER_CONFIGURATION_PROTOCOL` in
  the driver's entry point.

  If a driver must be compatible with EFI 1.10, and has any configurable options,
this protocol is required.

* Installs one or more instances of the `EFI_DRIVER_CONFIGURATION2_PROTOCOL` in
  the driver's entry point.

  If a driver must be compatible with UEFI 2.0 and has any configurable options,
this protocol is required.

* Install one or more instances of the `EFI_DRIVER_DIAGNOSTICS_PROTOCOL` in the
  driver's entry point.

  If a driver must be compatible with EFI 1.10 and provide diagnostics for the
controllers that the driver manages, this protocols is required.
