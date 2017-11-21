<!--- @file
  11.3.1 Device Drivers

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

### 11.3.1 Device Drivers

Device drivers implementing `GetControllerName()` must verify that
_ChildHandle_ is `NULL` and that _ControllerHandle_ represents a device the
device driver is currently managing. In addition, `GetControllerName()` must
verify that the requested _Language_ is in the set of languages the UEFI Driver
supports. The example below shows the steps required to check these parameters.
If the checks pass, the name of the controller is returned. In this specific
example, the driver opens the PCI I/O Protocol in its Driver Binding `Start()`
function. This is why `gEfiPciIoProtocolGuid` is used in the call to the EDK II
Library `UefiLib` function `EfiTestManagedDevice()` that checks to see if the
UEFI Drivers providing the `GetControllerName()` service is currently managing
_ControllerHandle_. Just like the `GetDriverName()` example in the previous
section, a static table of Unicode strings for the controller names is declared
as a global variable and the `LookupUnicodeString2()` service is used to lookup the name of the controller in the requested _Language_.

###### Example 131-GetControllerName() for a Device Driver

```c
#include <Uefi.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/PciIo.h>
#include <Library/UefiLib.h>

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_UNICODE_STRING_TABLE mAbcControllerNameTable[] = {
  { "eng;en", (CHAR16 *)L"ABC Controller in English"},
  { "fra;fr", (CHAR16 *)L"ABC Controller in French"},
  { "spa;sp", (CHAR16 *)L"ABC Controller in Spanish"},
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
  EFI_STATUS  Status;
  
  //
  // ChildHandle must be NULL for a Device Driver
  //
  if (ChildHandle != NULL) {
    return EFI_UNSUPPORTED;
  }
  
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
  
  return LookupUnicodeString2 (
           Language,
           This->SupportedLanguages,
           mAbcControllerNameTable,
           ControllerName,
           (BOOLEAN)(This != &gAbcComponentName2)
           );
}
```

If the private context structure is required, use the UEFI Boot Service
`OpenProtocol()`to open one of the protocols on _ControllerHandle_ produced by
the UEFI Driver and then use a `CR()` based macro to retrieve a pointer to the
private context structure.

Some device drivers can extract name information from the devices they manage
and are then able to provide more specific device names. The dynamic generation
of controller names does increase the complexity of the UEFI Driver
implementation, but it may provide users with the detailed information they
require to identify a specific device. For example, a driver for a mass storage
device may be able to produce a static name such as "Hard Disk," but a more
specific name, such as "XYZ Manufacturer SATA Model 123 Hard Disk", may be much
more useful.

To support the dynamic generation of controller names, a few additional steps
must be taken. First, a pointer to the dynamic table of names must be added to
the private context data structure for the controllers a device driver manages.
The example below shows the addition of an `EFI_UNICODE_STRING_TABLE` field to
the private context data structure discussed in _Chapter 8_ of this guide.

###### Example 132-Controller names in private context data structure

```c
#define ABC_PRIVATE_DATA_SIGNATURE SIGNATURE_32 ('A','B','C',' ')

typedef struct {
  UINTN                     Signature;
  EFI_PCI_IO_PROTOCOL       *PciIo;
  //
  // Dynamically allocated table of controller names
  //
  EFI_UNICODE_STRING_TABLE  *ControllerNameTable;
} ABC_PRIVATE_DATA;
#define ABC_PRIVATE_DATA_FROM_PCI_IO_THIS(a) \
CR (a, ABC_PRIVATE_DATA, PciIo, ABC_PRIVATE_DATA_SIGNATURE)
```

The next update is to the `Start()` service of the Driver Binding Protocol. It
needs to add a controller name in each supported language to
_ControllerNameTable_ in the private context data structure. Use the `UefiLib`
function `AddUnicodeString2()` to add one or more names to a table. The
_ControllerNameTable_ must be initialized to `NULL` before the first name is
added.

The following example shows the addition of an English name to a dynamically
allocated table of Unicode names. If more than one language is supported, then
`AddUnicodeString2()` is called for each language. The construction of the
Unicode string for each language is not covered here. The format of names
stored with devices varies depending on the bus type, and the translation from
a bus-specific name format to a Unicode string cannot be standardized.

###### Example 133-Adding a controller name to a dynamic controller name table

```c
#include <Uefi.h>
#include <Library/UefiLib.h>

ABC_PRIVATE_DATA *Private
CHAR16 *ControllerName

//
// Get dynamic name from the device being managed
//

//
// Convert the device name to a Unicode string in a supported language
//

//
// Add the device name to the table of names stored in the private
// context data structure using ISO 639-2 language code
//
AddUnicodeString2 (
  "eng",
  gAbcComponentName.SupportedLanguages,
  &Private->ControllerNameTable,
  ControllerName,
  TRUE
  );
  
//
// Add the device name to the table of names stored in the private
// context data structure using RFC 4646 language code
//
AddUnicodeString2 (
  "en",
  gAbcComponentName2.SupportedLanguages,
  &Private->ControllerNameTable,
  ControllerName,
  FALSE
  );
```

The `Stop()` service of the Driver Binding Protocol also needs to be updated.
When a request is made for a driver to stop managing a controller, the table of
controller names built in the `Start()` service must be freed. Use the UEFI
driver library function `FreeUnicodeStringTable()`to free the table of
controller names.

The code to add to the Driver Binding Protocol `Stop()` service _follows_. The
private context data structure is required by the `Stop()` service so the
private context data structure can be freed. The call to
`FreeUnicodeStringTable()`should be made just before the private context data
structure is freed.

###### Example 134-Freeing a dynamic controller name table

```c
#include <Uefi.h>
#include <Library/UefiLib.h>

ABC_PRIVATE_DATA *Private

FreeUnicodeStringTable (Private->ControllerNameTable);
```

Lastly, the `GetControllerName()` service is slightly different because the
dynamic table of controller names from the private context structure is used
instead of the static table of controller names. Because the table of
controller names is now maintained in the private context data structure, the
private context data structure needs to be retrieved based on the parameters
passed into `GetControllerName()`. This retrieval is achieved by looking up a
protocol that the driver has produced on _ControllerHandle_ and using a pointer
to that protocol and a `CR()` macro to retrieve a pointer to the private
context data structure. The private context data structure can then be used
with the `UefiLib` function `LookupUnicodeString2()` to look up the
controller's name in the dynamic table of controller names.

The example below shows the `GetControllerName()` service that retrieves the
controller name from a dynamic table stored in the private context data
structure.

###### Example 135-Device driver with dynamic controller names

```c
#include <Uefi.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/PciIo.h>
#include <Library/UefiLib.h>

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
  EFI_STATUS           Status;
  EFI_PCI_IO_PROTOCOL  *PciIo;
  ABC_PRIVATE_DATA     *Private;
  
  //
  // ChildHandle must be NULL for a Device Driver
  //
  if (ChildHandle != NULL) {
    return EFI_UNSUPPORTED;
  }
  
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
  
  //
  // Retrieve an instance of a produced protocol from ControllerHandle
  //
  Status = gBS->OpenProtocol (
                  ControllerHandle,
                  &gEfiPciIoProtocolGuid,
                  (VOID **)&PciIo,
                  gAbcDriverBinding.DriverBindingHandle,
                  ControllerHandle,
                  EFI_OPEN_PROTOCOL_GET_PROTOCOL
                  );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // Retrieve the private context data structure for ControllerHandle
  //
  Private = ABC_PRIVATE_DATA_FROM_PCI_IO_THIS (PciIo);
  
  //
  // Look up the controller name from a dynamic table of controller names
  //
  return LookupUnicodeString2 (
           Language,
           This->SupportedLanguages,
           Private->ControllerNameTable,
           ControllerName,
           (BOOLEAN)(This != &gAbcComponentName2)
           );
}
```
