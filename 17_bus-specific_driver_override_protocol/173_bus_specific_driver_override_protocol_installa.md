<!--- @file
  17.3 Bus Specific Driver Override Protocol Installation

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

## 17.3 Bus Specific Driver Override Protocol Installation

The example below shows a fragment from the Driver Binding Protocol `Start()`
that installs the Bus Specific Driver Override Protocol instance onto a child
handle produced by the bus driver.

###### Example 165-Install Bus Specific Driver Override Protocol

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/BusSpecificDriverOverride.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS EFIAPI
AbcStart (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN EFI_DEVICE_PATH_PROTOCOL     *RemainingDevicePath   OPTIONAL
  )
{
  EFI_STATUS        Status;
  ABC_PRIVATE_DATA  *Private;
  EFI_HANDLE        ChildHandle;

  . .
  
  Status = gBS->InstallMultipleProtocolInterfaces (
                  ChildHandle,
                  &gEfiBusSpecificDriverOverrideProtocolGuid,
                  &Private->BusSpecificDriverOverride,
                  NULL
                  );
  . .
}
```

The following example shows a fragment from the Driver Binding Protocol
`Stop()` function that uninstalls the Bus Specific Driver Override Protocol
instance from a child handle produced by the bus driver.

###### Example 166-Uninstall Bus Specific Driver Override Protocol

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/BusSpecificDriverOverride.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS 
EFIAPI
AbcStop (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN UINTN                        NumberOfChildren,
  IN EFI_HANDLE                   *ChildHandleBuffer
)
{
  EFI_STATUS        Status;
  ABC_PRIVATE_DATA  *Private;
  EFI_HANDLE        ChildHandle;

  . .
  
  Status = gBS->UninstallMultipleProtocolInterfaces (
                  ChildHandle,
                  &gEfiBusSpecificDriverOverrideProtocolGuid,
                  &Private->BusSpecificDriverOverride,
                  NULL
                  );
  . .
}
```
