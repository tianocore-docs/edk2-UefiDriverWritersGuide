<!--- @file
  16.1 Driver Supported EFI Version Protocol Implementation

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

## 16.1 Driver Supported EFI Version Protocol Implementation

The implementation of the Driver Supported EFI Version Protocol is typically
found in the `<<DriverName>>`.c file for a UEFI Driver and is installed onto
the ImageHandle in the driver entry point using the UEFI Boot Service
InstallMultipleProtocolInterfaces(). Appendix A contains a template for the
`<<DriverName>>`.c file that includes the declaration of a global variable for
the Driver Supported EFI Version Protocol instance. The list of tasks required
to implement the Driver Support EFI Version Protocol feature are as follows:
* Add global variable for the `EFI_DRIVER_SUPPORTED_EFI_VERSION_PROTOCOL`
  instance to `<<DriverName>>.c`
* Set the _FirmwareVersion_ field of the Driver Supported EFI Version Protocol
  instance in the driver entry point if the value required is different than
  the value assigned in the global variable declaration.
* Install the Driver Supported EFI Version Protocol instance onto the
  _ImageHandle_ of the UEFI Driver in the driver entry point.
* If the UEFI Driver supports the unload feature, uninstall the Driver
  Supported EFI Version Protocol instance in the _Unload()_ function.

The following example shows the protocol interface structure for the Driver
Supported EFI Version Protocol for reference. It is composed of the two data
fields called _Length_ and _FirmwareVersion_.

###### Example 160-Driver Support EFI Version Protocol

```c
///
/// The EFI_DRIVER_SUPPORTED_EFI_VERSION_PROTOCOL provides a
/// mechanism for an EFI driver to publish the version of the EFI
/// specification it conforms to. This protocol must be placed on
/// the driver's image handle when the driver's entry point is
/// called.
///
typedef struct _EFI_DRIVER_SUPPORTED_EFI_VERSION_PROTOCOL {
  ///
  /// The size, in bytes, of the entire structure. Future versions of this
  /// specification may grow the size of the structure.
  ///
  UINT32 Length; 
  ///
  /// The version of the EFI specification that this driver conforms to.
  ///
  UINT32 FirmwareVersion;
} EFI_DRIVER_SUPPORTED_EFI_VERSION_PROTOCOL;
```

This example declares a global variable called `gEbcDriverSupportedEfiVersion`
whose _FirmwareVersion_ field is assigned to `EFI_2_31_SYSTEM_TABLE_REVISION`,
the value associated with the _UEFI 2.3.1 Specification_.

###### Example 161-Driver Supported EFI Version Protocol Feature

```c
#include <Uefi.h>
#include <Protocol/DriverSupportedEfiVersion.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/DebugLib.h>

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_DRIVER_SUPPORTED_EFI_VERSION_PROTOCOL gAbcDriverSupportedEfiVersion = {
  sizeof (EFI_DRIVER_SUPPORTED_EFI_VERSION_PROTOCOL),  // Length
  EFI_2_31_SYSTEM_TABLE_REVISION                       // FirmwareVersion
};

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE                                        ImageHandle,
  IN EFI_SYSTEM_TABLE                                  *SystemTable
  )
{
  EFI_STATUS  Status;
  
  //
  // Install Driver Supported EFI Version Protocol onto ImageHandle
  //
  Status = gBS->InstallMultipleProtocolInterfaces (
                  &ImageHandle,
                  &gEfiDriverSupportedEfiVersionProtocolGuid,
                  & gAbcDriverSupportedEfiVersion,
                  NULL
                  );
  ASSERT_EFI_ERROR (Status);
  
  return Status;
}
```

The EFI System Table chapter of the _UEFI Specification_ defines revision
values for the _EFI Specifications_ and _UEFI Specifications_. The table below
provides a summary of the define name available to UEFI Drivers.

###### Table 24-UEFI Specific Revision Values

| **Specification**                  | **Define Name**                  | **Value**               |
| ---------------------------------- | -------------------------------- | ----------------------- |
| _UEFI Specification Version 2.3.1_ | `EFI_2_31_SYSTEM_TABLE_REVISION` | ((2 << 16) &#124; (31)) |
| _UEFI Specification Version 2.3_   | `EFI_2_30_SYSTEM_TABLE_REVISION` | ((2 << 16) &#124; (30)) |
| _UEFI Specification Version 2.2_   | `EFI_2_20_SYSTEM_TABLE_REVISION` | ((2 << 16) &#124; (20)) |
| _UEFI Specification Version 2.1_   | `EFI_2_10_SYSTEM_TABLE_REVISION` | ((2 << 16) &#124; (10)) |
| _UEFI Specification Version 2.0_   | `EFI_2_00_SYSTEM_TABLE_REVISION` | ((2 << 16) &#124; (00)) |
| _EFI Specification Version 1.1_    | `EFI_1_10_SYSTEM_TABLE_REVISION` | ((1 << 16) &#124; (10)) |
| _EFI Specification Version 1.02_   | `EFI_1_02_SYSTEM_TABLE_REVISION` | ((1 << 16) &#124; (02)) |

UEFI Drivers producing the Driver Supported EFI Version Protocol typically use
the style shown in the example above. However, more complex UEFI Drivers
compatible with several versions of the _EFI Specification_ and _UEFI
Specification_ must detect the UEFI capabilities of the platform firmware and
adjust the behavior of the UEFI Driver to match those UEFI capabilities. In
this more complex case, the UEFI Driver updates the _FirmwareVersion_ field to
declare the specific version of the _UEFI Specification_ the UEFI Driver
follows.
