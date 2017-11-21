<!--- @file
  15.1 Driver Family Override Protocol Implementation

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

## 15.1 Driver Family Override Protocol Implementation

The implementation of the Driver Family Override Protocol is typically found
in the file DriverFamilyOverride.c. Appendix A contains a template for a
DriverFamilyOverride.c file for a UEFI Driver. The list of tasks to implement
the Driver Family Override Protocol feature follow:
* Add global variable for the `EFI_DRIVER_FAMILY_OVERRIDE_PROTOCOL` instance to
  `DriverFamilyOverride.c`.
* Implement the `GetVersion()` service of the Driver Family Override Protocol
  in `DriverFamilyOverride.c`.
* Install the Driver Family Override Protocol onto the same handle that the
  Driver Binding Protocol is installed.
* If the UEFI Driver produces multiple Driver Binding Protocols, install the
  Driver Family Override Protocol on the same handles as that of the Driver
  Binding Protocol.
* If the UEFI Driver supports the unload feature, uninstall all the Driver
  Family Override Protocol instances in the `Unload()` function.

The Driver Family Override Protocol contains one service called `GetVersion()`
that returns version value used by the UEFI Boot Service `ConnectController()`
to determine the order of Driver Binding Protocol used to start a specific
controller. If the Driver Family Override Protocol is present, it is higher
priority than the Bus Specific Driver Override Protocol, but lower than the Platform Driver Override Protocol. See the _Chapter 3_ and the Protocol Handler Services section of the _UEFI
Specification_ for details on how the UEFI Boot Service `ConnectController()` selects the best UEFI Driver to manage a specific controller.

For reference, the example below shows the protocol interface structure for the
Driver Family Override Protocol. It is composed of a single service called
`GetVersion()`.

###### Example 157-Driver Family Override Protocol

```c
typedef struct _EFI_DRIVER_FAMILY_OVERRIDE_PROTOCOL
  EFI_DRIVER_FAMILY_OVERRIDE_PROTOCOL;

///
/// When installed, the Driver Family Override Protocol produces a GUID that
/// represents a family of drivers. Drivers with the same GUID are members of
/// the same family. When drivers are connected to controllers, drivers with a
/// higher revision value in the same driver family are connected with a higher
/// priority than drivers with a lower revision value in the same driver family.
/// The EFI Boot Service ConnectController() uses five rules to build a prioritized
/// list of drivers when a request is made to connect a driver to a controller.
/// The Driver Family Protocol rule is between the Platform Specific Driver
/// Override Protocol and above the Bus Specific Driver Override Protocol.
///
struct _EFI_DRIVER_FAMILY_OVERRIDE_PROTOCOL {
  EFI_DRIVER_FAMILY_OVERRIDE_GET_VERSION GetVersion;
};
```

The following example declares a global variable called `gAbcDriverFamilyOverride` with the single service called `AbcGetVersion()`. The UEFI Boot Service `InstallMultipleProtocolInterfaces()` is used to install the Driver Family
Override Protocol instance `gAbcDriverFamilyOverride` onto the same
_ImageHandle_ as which the Driver Binding Protocol instance `gAbcDriverBinding`
is installed.

###### Example 158-Install Driver Family Override Protocol

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/DriverFamilyOverride.h>
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
EFI_DRIVER_FAMILY_OVERRIDE_PROTOCOL gAbcDriverFamilyOverride = {
  AbcGetVersion
};

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE                  ImageHandle,
  IN EFI_SYSTEM_TABLE            *SystemTable
  )
{
  EFI_STATUS Status;
  //
  // Install driver model protocol(s) on ImageHandle
  //
  Status = EfiLibInstallDriverBinding (
             ImageHandle,        // ImageHandle
             SystemTable,        // SystemTable
             &gAbcDriverBinding, // DriverBinding
             ImageHandle         // DriverBindingHandle
             );
  ASSERT_EFI_ERROR (Status);
  
  //
  // Install Driver Family Override Protocol onto ImageHandle
  //
  Status = gBS->InstallMultipleProtocolInterfaces (
                  &ImageHandle,
                  &gEfiDriverFamilyOverrideProtocolGuid,
                  &gAbcDriverFamilyOverride,
                  NULL
                  );
  ASSERT_EFI_ERROR (Status);
  
  return Status;
}
```
