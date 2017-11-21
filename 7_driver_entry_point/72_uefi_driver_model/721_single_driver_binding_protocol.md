<!--- @file
  7.2.1 Single Driver Binding Protocol

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

### 7.2.1 Single Driver Binding Protocol

The following is an example of the entry point to the `Abc` driver that
installs the Driver Binding Protocol `gAbcDriverBinding`, the Component Name 2
Protocol `gAbcComponentName2`, and the Component Name Protocol `gAbcComponentName` onto
the `Abc` driver's image handle and does not install any of the other optional
driver-related protocols or features. This driver returns the status from the
UEFI driver library function `EfiLibInstallDriverBindingComponentName2()`. See
_Chapter 9_ for details on the services and data fields produced by the Driver
Binding Protocol and _Chapter 11_ for details on the Component Name 2 Protocol
and the Component Name Protocol.

Notice that the Component Name Protocol and the Component Name 2 Protocol use
the same function pointers for their services called `AbcGetDriverName()` and
`AbcGetControllerName()`. This is a size reduction technique supported by the
EDK II that reduces the overhead for a single UEFI Driver to support both
Component Name Protocols.

Also note that the optional protocol structures are declared with
`GLOBAL_REMOVE_IF_UNREFERENCED`. This style allows these structures and the
associated services to be removed if the Component Name feature is disabled
when this UEFI driver is compiled. The EDK II library `UefiLib` uses several
Platform Configuration Database (PCD) feature flags to enable and disable the
Component Name Protocols and Driver Diagnostics Protocols at build time. This
allows a developer to implement all of these in the UEFI Driver sources and
select the ones that are actually needed for a specific release at build time.
_Chapter 30_ covers how to build UEFI Drivers in the EDK II and also covers
configuration of UEFI Drivers through PCD settings.

**********
**Note:** _This example and many other examples throughout this guide make use
of the EDK II library `DebugLib` that provides `DEBUG()` and `ASSERT()` related macros. These macros are very useful during development
and debug. However, `ASSERT()` related macros must be disabled when UEFI
Drivers are released. Chapter 31 covers the PCD setting to enable and disable
the macros provided by `DebugLib`._
**********

###### Example 89-Single Driver Binding Protocol

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/ComponentName.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/UefiLib.h>
#include <Library/DebugLib.h>

#define ABC_VERSION 0x10

EFI_DRIVER_BINDING_PROTOCOL gAbcDriverBinding = {
  AbcSupported,
  AbcStart,
  AbcStop,
  ABC_VERSION,
  NULL,
  NULL
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME_PROTOCOL gAbcComponentName = {
  (EFI_COMPONENT_NAME_GET_DRIVER_NAME) AbcGetDriverName,
  (EFI_COMPONENT_NAME_GET_CONTROLLER_NAME) AbcGetControllerName,
  "eng"
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME2_PROTOCOL gAbcComponentName2 = {
  AbcGetDriverName,
  AbcGetControllerName,
  "en"
};

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE                      ImageHandle,
  IN EFI_SYSTEM_TABLE                *SystemTable
  )
{
  EFI_STATUS  Status;
  
  //
  // Install driver model protocol(s) onto ImageHandle
  //
  Status = EfiLibInstallDriverBindingComponentName2 (
             ImageHandle,            // ImageHandle
             SystemTable,            // SystemTable
             &gAbcDriverBinding,     // DriverBinding
             ImageHandle,            // DriverBindingHandle
             &gAbcComponentName,     // ComponentName
             &gAbcComponentName2     // ComponentName2
             );
  ASSERT_EFI_ERROR (Status);
  
  return Status;
}
```

The following example shows another example of the entry point to the `Abc`
driver that installs the Driver Binding Protocol `gAbcDriverBinding` onto the
`Abc` driver's image handle and the optional driver-related protocols. This
driver returns the status from the UEFI driver library function
`EfiLibInstallAllDriverProtocols2()`. This library function is used if one or
more of the optional driver related protocols are being installed.

See Chapters _9_, _11_, _12_, and _13_ for details on the services and data
fields produced by the Driver Binding Protocol, Component Name Protocols,
Driver Configuration Protocols, and Driver Diagnostics Protocols.

###### Example 90-Single Driver Binding Protocol with optional features

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/ComponentName.h>
#include <Protocol/DriverDiagnostics.h>
#include <Protocol/DriverDiagnostics2.h>
#include <Protocol/DriverConfiguration.h>
#include <Protocol/DriverConfiguration2.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/UefiLib.h>
#include <Library/DebugLib.h>

#define ABC_VERSION 0x10

EFI_DRIVER_BINDING_PROTOCOL gAbcDriverBinding = {
  AbcSupported,
  AbcStart,
  AbcStop,
  ABC_VERSION,
  NULL,
  NULL
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME_PROTOCOL gAbcComponentName = {
  (EFI_COMPONENT_NAME_GET_DRIVER_NAME) AbcGetDriverName,
  (EFI_COMPONENT_NAME_GET_CONTROLLER_NAME) AbcGetControllerName,
  "eng"
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME2_PROTOCOL gAbcComponentName2 = {
  AbcGetDriverName,
  AbcGetControllerName,
  "en"
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_DRIVER_CONFIGURATION_PROTOCOL gAbcDriverConfiguration = {
  (EFI_DRIVER_CONFIGURATION_SET_OPTIONS) AbcDriverConfigurationSetOptions,
  (EFI_DRIVER_CONFIGURATION_OPTIONS_VALID) AbcDriverConfigurationOptionsValid,
  (EFI_DRIVER_CONFIGURATION_FORCE_DEFAULTS)AbcDriverConfigurationForceDefaults,
  "eng"
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_DRIVER_CONFIGURATION2_PROTOCOL gAbcDriverConfiguration2 = {
  AbcDriverConfigurationSetOptions,
  AbcDriverConfigurationOptionsValid,
  AbcDriverConfigurationForceDefaults,
  "en"
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_DRIVER_DIAGNOSTICS_PROTOCOL gAbcDriverDiagnostics = {
  (EFI_DRIVER_DIAGNOSTICS_RUN_DIAGNOSTICS) AbcRunDiagnostics,
  "eng"
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_DRIVER_DIAGNOSTICS2_PROTOCOL gAbcDriverDiagnostics2 = {
  AbcRunDiagnostics,
  "en"
};

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE                         ImageHandle,
  IN EFI_SYSTEM_TABLE                   *SystemTable
  )
{
  EFI_STATUS  Status;
  
  //
  // Install driver model protocol(s) onto ImageHandle.
  //
  Status = EfiLibInstallAllDriverProtocols2 (
             ImageHandle,               // ImageHandle
             SystemTable,               // SystemTable
             &gAbcDriverBinding,        // DriverBinding
             ImageHandle,               // DriverBindingHandle
             &gAbcComponentName,        // ComponentName2
             &gAbcComponentName2,       // ComponentName2
             &gAbcDriverConfiguration,  // DriverConfiguration
             &gAbcDriverConfiguration2, // DriverConfiguration2
             &gAbcDriverDiagnostics,    // DriverDiagnostics
             &gAbcDriverDiagnostics2    // DriverDiagnostics2
             );
  ASSERT_EFI_ERROR (Status);
  
  return Status;
}
```
