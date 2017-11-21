<!--- @file
  6.5 Root Bridge Drivers

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

## 6.5 Root Bridge Drivers

A root bridge driver does not produce any instances of the `EFI_DRIVER_BINDING_PROTOCOL`. It is responsible for initializing and immediately creating physical controller handles for the root bridge
controllers in a platform. It is the UEFI driver's responsibility to install
the Device Path Protocol onto the physical controller handles because the root
bridge controllers represent physical devices.

The most common example of a root bridge driver is a driver that produces the
PCI Root Bridge I/O Protocol and a Device Path Protocol for each PCI Root
Bridge in a platform supporting PCI.

A list of features a root bridge driver can optionally implement follows.
Recommended and optional features are noted below.

* Register one or more HII packages in the driver's entry point.

These HII packages provide strings, fonts, and forms that allow users (such as
IT administrators) to change the driver's configuration. These HII packages are
required only if a driver must provide the ability for a user to change
configuration settings for a device.

* Install one or more instances of the `EFI_HII_CONFIG_ACCESS_PROTOCOL` in the
  driver's entry point.

This protocol provides the services to save and restore configuration settings
for a device. It is required only if a driver must provide the ability for a
user to change configuration settings for a device.

* Install an instance of the `EFI_DRIVER_SUPPORTED_EFI_VERSION_PROTOCOL` in the
  driver's entry point.

The _UEFI Specification_ requires this protocol for PCI controllers or other
plug-in cards. Even though this requirement does not apply to Root Bridge
Drivers, implementation of this feature is still `recommended`.
