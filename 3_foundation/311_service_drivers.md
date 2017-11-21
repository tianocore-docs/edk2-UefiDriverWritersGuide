<!--- @file
  3.11 Service Drivers

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

## 3.11 Service Drivers

A service driver does not manage any devices and does not produce any instances
of the `EFI_DRIVER_BINDING_PROTOCOL`. It is a simply a driver that produces one
or more protocols on one or more new service handles in the handle database.
These service handles do not have a Device Path Protocol because they do not
represent physical devices. The driver entry point returns `EFI_SUCCESS` after
the service handles are created and the protocols installed, leaving the driver
resident in system memory. Some example service drivers in the `MdeModulePkg`
in the EDK II include:

* `MdeModulePkg/Universal/Acpi/AcpiTableDxe`

* `MdeModulePkg/Universal/DebugSupportDxe`

* `MdeModulePkg/Universal/DevicePathDxe`

* `MdeModulePkg/Universal/EbcDxe`

* `MdeModulePkg/Universal/HiiDatabaseDxe`

* `MdeModulePkg/Universal/PrintDxe`

* `MdeModulePkg/Universal/SetupBrowserDxe`

* `MdeModulePkg/Universal/SmbiosDxe`
