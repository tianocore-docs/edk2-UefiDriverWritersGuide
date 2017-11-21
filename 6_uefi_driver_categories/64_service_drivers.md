<!--- @file
  6.4 Service Drivers

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

## 6.4 Service Drivers

A service driver does not manage any devices nor does it produce any instances
of the `EFI_DRIVER_BINDING_PROTOCOL`. It is a simple driver that produces one
or more protocols on one or more new service handles. These service handles do
not have a Device Path Protocol because they do not represent physical devices.
The driver entry point returns `EFI_SUCCESS` after the service handles are
created and the protocols are installed, leaving the driver resident in system
memory. The list of features that a service driver can optionally implement
follows. Recommended and optional features are noted below.

* Register one or more HII packages in the driver's entry point.

These HII packages provide strings, fonts, and forms that allow users (such as
IT administrators) to change the driver's configuration. These HII packages are
required only if a driver must provide the ability for a user to change
configuration settings.

* Install one or more instances of the `EFI_HII_CONFIG_ACCESS_PROTOCOL` in the
  driver's entry point.

This protocol provides the services to save and restore configuration settings
for a device. This protocol is required only if a driver must provide the
ability for a user to change configuration settings.

* Install an instance of the `EFI_DRIVER_SUPPORTED_EFI_VERSION_PROTOCOL` in the
  driver's entry point.

The _UEFI Specification_ requires this protocol for PCI controllers or other
plug-in cards. Even though this requirement does not apply to Service Drivers,
implementation of this feature is still `recommended`.
