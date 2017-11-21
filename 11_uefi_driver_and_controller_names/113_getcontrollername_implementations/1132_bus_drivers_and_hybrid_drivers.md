<!--- @file
  11.3.2 Bus Drivers and Hybrid Drivers

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

### 11.3.2 Bus Drivers and Hybrid Drivers

There are many levels of support a bus driver or hybrid driver may provide for
the Component Name Protocols. These drivers can choose to provide a driver name
as described in the section of this chapter on `GetDriverName()`. They can also
choose to provide names for the bus controllers they manage and to not provide
any names for the children they produce (such as the device drivers described
the previous section). This discussion explains what bus drivers and hybrid
drivers need to do to provide human-readable names for the child handles they
produce. The human-readable names for child handles can be provided through
static or dynamic controller name tables.

**********
**Note:** _It is **recommended** that bus drivers and hybrid drivers provide
controller names for both the bus controller and the child controllers these
types of drivers produce. Implementing controller names for only the bus
controller or only the child controllers is discouraged._
**********

Bus drivers and hybrid drivers implementing the Component Name Protocols must
verify that _ControllerHandle_ and _ChildHandle_ represent a device the driver
is currently managing. In addition, `GetControllerName()` must verify the
requested _Language_ is in the set of languages the UEFI Driver supports. The
following example shows the steps required to check these parameters. If these
checks pass, the controller name is returned in the requested language. In this
specific example, the driver opens the PCI I/O Protocol in its Driver Binding
Start() function. This is why `gEfiPciIoProtocolGuid` is used in the call to
the EDK II Library `UefiLib` function `EfiTestManagedDevice()` that checks to
see if the UEFI Drivers providing the `GetControllerName()` service is
currently managing _ControllerHandle_. If the private context structure is
required, then typically the UEFI Boot Service `OpenProtocol()` is used to open
one of the protocols on _ControllerHandle_ that the UEFI Driver produced and
then uses a `CR()` based macro to retrieve a pointer to the private context
structure.

**********
**Note:** _If ChildHandle is NULL, a request is made for the name of the bus
controller. If ChildHandle is not NULL, a request is made for the name of a
child controller managed by the UEFI Driver._
**********

###### Example 136-GetControllerName() for a Bus Driver or Hybrid Driver

```c
#include <Uefi.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/PciIo.h>
#include <Library/UefiLib.h>

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_UNICODE_STRING_TABLE mAbcControllerNameTable[] = {
  { "eng;en", (CHAR16 *)L"ABC Bus Controller in English"},
  { "fra;fr", (CHAR16 *)L"ABC Bus Controller in French"},
  { "spa;sp", (CHAR16 *)L"ABC Bus Controller in Spanish"},
  { NULL, NULL }
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_UNICODE_STRING_TABLE mAbcChildNameTable[] = {
  { "eng;en", (CHAR16 *)L"ABC Child Controller in English"},
  { "fra;fr", (CHAR16 *)L"ABC Child Controller in French"},
  { "spa;sp", (CHAR16 *)L"ABC Child Controller in Spanish"},
  { NULL, NULL }
};

EFI_STATUS
EFIAPI
AbcGetControllerName (
  IN  EFI_COMPONENT_NAME2_PROTOCOL  *This,
  IN  EFI_HANDLE                    ControllerHandle,
  IN  EFI_HANDLE                    ChildHandle,       OPTIONAL
  IN  CHAR8                         *Language,
  OUT CHAR16                        **ControllerName
  )
{
  EFI_STATUS                        Status;
  EFI_UNICODE_STRING_TABLE          *NameTable;
  
  //
  // Make sure this driver is currently managing ControllerHandle
  //
  Status = EfiTestManagedDevice (
             ControllerHandle,
             gAbcDriverBinding.DriverBindingHandle,
             &gEfiPciIoProtocolGuid
             );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  if (ChildHandle == NULL) {
    NameTable = mAbcControllerNameTable;
  } else {
    //
    // If ChildHandle is not NULL, then make sure this driver produced ChildHandle
    //
    Status = EfiTestChildHandle (
               ControllerHandle,
               ChildHandle,
               &gEfiPciIoProtocolGuid
               );
    if (EFI_ERROR (Status)) {
      return Status;
    }
    NameTable = mAbcChildNameTable;
  }
  
  return LookupUnicodeString2 (
           Language,
           This->SupportedLanguages,
           NameTable,
           ControllerName,
           (BOOLEAN)(This != &gAbcComponentName2)
           );
}
```

The static tables for the controller names and the child names can be
substituted with dynamic tables. This substitution requires the private context
structure to be updated along with the `Start()` and `Stop()` services of the
Driver Binding Protocol. The previous section explains how this update is done
for the controller names. The exact same technique can be applied to child
controllers.
