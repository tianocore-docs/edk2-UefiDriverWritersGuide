<!--- @file
  7.2 UEFI Driver Model

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

## 7.2 UEFI Driver Model

Drivers that follow the UEFI driver model are not allowed to touch any hardware
in their driver entry point. In fact, these types of drivers do very little in
their driver entry point.

They are required to register protocol interfaces in the Handle Database and
may also choose to register HII packages in the HII Database, register an
`Unload()` service in the UEFI Driver's Loaded Image Protocol, and create
events that are signaled when an operating system calls `ExitBootServices()` or
`SetVirtualAddressMap()`. This design allows these types of drivers to be
loaded at any point in the system initialization sequence because their driver
entry points depend only on a few of the UEFI boot services. The items
registered in the driver entry point are used later in the boot sequence to
initialize, configure, or diagnose devices required to boot an operating system.

All UEFI drivers following the UEFI driver model must install one or more
instances of the Driver Binding Protocol onto handles in the handle database.
The first Driver Binding Protocol is typically installed onto the _ImageHandle_
passed into the UEFI Driver entry point. Additional instances of the Driver
Binding Protocol must be installed onto new handles in the Handle Database.

The EDK II library `UefiLib` provides four functions that simplify the
implementation of the driver entry point of a UEFI driver. The examples in this
section make use of these driver initialization functions as shown in the
following example.

###### Example 88-EDK II UefiLib driver initialization functions

```c
EFI_STATUS
EFIAPI
EfiLibInstallDriverBinding (
  IN CONST EFI_HANDLE ImageHandle,
  IN CONST EFI_SYSTEM_TABLE       *SystemTable,
  IN EFI_DRIVER_BINDING_PROTOCOL  *DriverBinding,
  IN EFI_HANDLE DriverBindingHandle
  );

EFI_STATUS
EFIAPI
EfiLibInstallAllDriverProtocols (
  IN CONST EFI_HANDLE                                  ImageHandle,
  IN CONST EFI_SYSTEM_TABLE                            *SystemTable,
  IN EFI_DRIVER_BINDING_PROTOCOL                       *DriverBinding,
  IN EFI_HANDLE                                        DriverBindingHandle,
  IN CONST EFI_COMPONENT_NAME_PROTOCOL                 *ComponentName,
  OPTIONAL IN CONST EFI_DRIVER_CONFIGURATION_PROTOCOL  *DriverConfiguration, OPTIONAL
  IN CONST EFI_DRIVER_DIAGNOSTICS_PROTOCOL             *DriverDiagnostics OPTIONAL
  );

EFI_STATUS
EFIAPI
EfiLibInstallDriverBindingComponentName2 (
  IN CONST EFI_HANDLE                    ImageHandle,
  IN CONST EFI_SYSTEM_TABLE              *SystemTable,
  IN EFI_DRIVER_BINDING_PROTOCOL         *DriverBinding,
  IN EFI_HANDLE                          DriverBindingHandle,
  IN CONST EFI_COMPONENT_NAME_PROTOCOL   *ComponentName, OPTIONAL
  IN CONST EFI_COMPONENT_NAME2_PROTOCOL  *ComponentName2 OPTIONAL
  );

EFI_STATUS
EFIAPI
EfiLibInstallAllDriverProtocols2 (
  IN CONST EFI_HANDLE                          ImageHandle,
  IN CONST EFI_SYSTEM_TABLE                    *SystemTable,
  IN EFI_DRIVER_BINDING_PROTOCOL               *DriverBinding,
  IN EFI_HANDLE                                DriverBindingHandle,
  IN CONST EFI_COMPONENT_NAME_PROTOCOL         *ComponentName, OPTIONAL
  IN CONST EFI_COMPONENT_NAME2_PROTOCOL        *ComponentName2, OPTIONAL
  IN CONST EFI_DRIVER_CONFIGURATION_PROTOCOL   *DriverConfiguration, OPTIONAL
  IN CONST EFI_DRIVER_CONFIGURATION2_PROTOCOL  *DriverConfiguration2, OPTIONAL
  IN CONST EFI_DRIVER_DIAGNOSTICS_PROTOCOL     *DriverDiagnostics, OPTIONAL
  IN CONST EFI_DRIVER_DIAGNOSTICS2_PROTOCOL    *DriverDiagnostics2 OPTIONAL
  );
```

`EfiLibInstallDriverBinding()` installs the Driver Binding Protocol onto the
handle specified by _DriverBindingHandle_. _DriverBindingHandle_ is typically
the same as _ImageHandle_, but if it is `NULL`, the Driver Binding Protocol is
installed onto a newly created handle. This function is typically used by a
UEFI Driver that does not implement any of the optional driver features.

`EfiLibInstallAllDriverProtocols()` installs the Driver Binding Protocol, and
the driverrelated protocols from the older _UEFI Specification_ (and _EFI
Specification_), onto the handle specified by _DriverBindingHandle_. The
optional driver-related protocols are defined as `OPTIONAL` because they can be
`NULL` if a driver is not producing that specific optional protocol. Once
again, the _DriverBindingHandle_ is typically the same as _ImageHandle_, but if
it is `NULL`, all driver-related protocols are installed onto a newly created
handle. This function is typically used by a UEFI Driver required to be
compatible with the _EFI 1.10 Specification_.

`EfiLibInstallDriverBindingComponentName2()` installs the Driver Binding
Protocol and the Component Name Protocols onto the handle specified by
_DriverBindingHandle_. The optional driver-related protocols are defined as
`OPTIONAL` because they can be `NULL` if a driver is not producing that
specific optional protocol. Once again, the _DriverBindingHandle_ is typically the same as _ImageHandle_, but if it is
`NULL`, all driverrelated protocols are installed onto a newly created handle.
This function is typically used by a UEFI Driver that implements the Component
Name Protocols that are `strongly recommended`.

`EfiLibInstallAllDriverProtocols2()` installs the Driver Binding Protocol,
Component Name Protocols, Driver Configuration Protocols, and Driver Diagnostics
Protocols onto the handle specified by _DriverBindingHandle_. The optional
driver-related protocols are defined as `OPTIONAL` because they can be `NULL`
if a driver is not producing that specific optional protocol. Once again, the
_DriverBindingHandle_ is typically the same as _ImageHandle_, but if it is
`NULL`, all driver-related protocols are installed onto a newly created handle.
This function is typically used by a UEFI Driver required to be compatible with
all versions of the _UEFI Specification_ and _EFI Specification_.
