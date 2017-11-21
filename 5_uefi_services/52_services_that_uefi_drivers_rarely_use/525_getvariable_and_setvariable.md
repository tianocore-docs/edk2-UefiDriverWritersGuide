<!--- @file
  5.2.5 GetVariable() and SetVariable()

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

### 5.2.5 GetVariable() and SetVariable()

Use `GetVariable()` and `SetVariable()` services to read and write UEFI
variables. UEFI Drivers for add-in adapters, such as PCI adapters, should not
use these services to access configuration information for the adapter.
Instead, the add-in adapter should provide its own local storage for
configuration information. UEFI Drivers provided with UEFI system firmware use
UEFI variables to store configuration information. Examples found in the EDK II
of UEFI Drivers use UEFI variables to store configuration information include the IPv4 and IPv6 network stacks in the
`MdeModulePkg/Universal/Network` and the `NetworkPkg`.

**********
**Caution:** Add-in cards should not store their configuration via variables.
When the card is removed from the system, the variables related to its
configuration become ownerless. There is no way to safely recover that data. In
addition, it is impossible for the system designer to determine the amount of
configuration data each card consumes. As such, there may simply not be enough
space to store the configuration in a particular system's variable space. To
ensure proper function, each card must store its own configuration on the
add-in card.
**********

A UEFI Variable is specified with a combination of a GUID and a Unicode string.
The GUID prevents name collisions between different vendors. Each vendor may create
GUIDs for their own storage and manage their own namespace of Unicode strings
for the GUID they create. The Boot Manager chapter of the _UEFI Specification_
defines the EFI_GLOBAL_VARIABLE_GUID, also known as `gEfiGlobalVariableGuid` in
the EDK II, that is reserved for UEFI variables defined by the _UEFI
Specification_. UEFI Drivers must never use this GUID to store their
configuration information.

**********
**Caution:** UEFI Drivers must never use **EFIGLOBAL_VARIBLE GUID** or
**gEfiGlobalVariableGuid** to store configuration information. This GUID is
reserved for use by the UEFI Specification._
**********

When UEFI variables are stored, there are attributes that describe the
visibility and persistence of each variable. The legal combinations of
attributes include the following:

* `BOOTSERVICE_ACCESS`
   - The variable is available for read and write access in the pre-boot environment
before `ExitBootServices()` is called. The variable is not available after
ExitBootServices() is called, and contents are also lost on the next system reset or power cycle. These types of variables are typically used to share information among different pre-boot components.


* `BOOTSERVICE_ACCESS | RUNTIME_ACCESS`
   - The variable is available for read and write access in the pre-boot environment before ExitBootServices() is called. and is available for read-only access from the OS runtime environment after ExitBootServices() is called. The contents are lost on the next system reset or power cycle. These types of variable are typically used to share information among different pre-boot components and pass read-only information to the operating system.


* `NON_VOLATILE | BOOTSERVICE_ACCESS`
   - The variable is available for read and write access in the pre-boot environment
before `ExitBootServices()` is called and the contents are persistent across
system resets and power cycles. These types of variables are typically used to
share persistent information among different pre-boot components.


* `NON_VOLATILE | BOOTSERVICE_ACCESS | RUNTIME_ACCESS`
   - The variable is available for read and write access in both the pre-boot
environment and the OS runtime environment and the contents are persistent
across system resets and power cycles. These types of variables are typically
used to share persistent information among pre-boot components and the
operating system.


A UEFI Driver that is required to use UEFI variables to store configuration
information typically accesses those UEFI variables in the implementation of
the services provided by a `EFI_HII_CONFIG_ACCESS_PROTOCOL` protocol instance.
The services `GetVariable()` and `SetVariable()` are used to get and set
configuration information associated with HII setup screens provided by the
UEFI Driver using the UEFI HII infrastructure that is described in more detail
in _Chapter 12_.

The attribute of NON_VOLATILE | BOOTSEVICE_ACCESS | RUNTIME_ACCESS is used to store configuration information that persists across resets and power cycles. It also allows for updates to this configuration information from operating systems that provide support for OS-present configuration changes using the HII database exported by the UEFI system firmware.

The attribute of `BOOTSERVICE_ACCESS` should be used with a UEFI variable used
as a mailbox to store state information that is required by multiple HII forms
or multiple HII callbacks.

The following code fragment shows how to write a configuration structure to a
UEFI variable whose contents are preserved across resets and power cycles. The
GUID value, GUID global variable, and the configuration structure associated
with the GUID are all typically declared in a GUID include file in an EDK II
package implemented by a vendor. The structure `EXAMPLE_CONFIGURATION` from

###### Example 65-Write configuration structure to a UEFI variable

```c
< Guid / ExampleConfigurationVariable.h > is shown here in comments to provide additional context for this specific code
fragment.
#include <Uefi.h>
#include <Library/UefiRuntimeServicesTableLib.h>
#include <Guid/ExampleConfigurationVariable.h>

//
// Example configuration structure from ExampleConfigurationVariable.h
//
//typedef struct {
// UINT32 Question1;
// UINT16 Question2;
// UINT8 Question3;
//} EXAMPLE_CONFIGURATION;

EFI_STATUS Status;
EXAMPLE_CONFIGURATION ExampleConfiguration;

Status = gRT->SetVariable (
                L"ExampleConfiguration",               // VariableName
                &gEfiExampleConfigurationVariableGuid, // VendorGuid
                EFI_VARIABLE_NON_VOLATILE |
                EFI_VARIABLE_BOOTSERVICE_ACCESS |
                EFI_VARIABLE_RUNTIME_ACCESS,
                                                       // Attributes 
				sizeof (EXAMPLE_CONFIGURATION),        // DataSize
                &ExampleConfiguration                  // Data
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

The code fragment below shows how to use the `GetVariable()` service to read
the configuration structure from the UEFI variable written in the previous
example.

###### Example 66-Read configuration structure from a UEFI variable

```c
#include <Uefi.h>
#include <Library/UefiRuntimeServicesTableLib.h>
#include <Guid/ExampleConfigurationVariable.h>

EFI_STATUS Status;
EXAMPLE_CONFIGURATION ExampleConfiguration;
UINTN DataSize;
UINT32 Attributes;

DataSize = sizeof (EXAMPLE_CONFIGURATION);
Attributes = EFI_VARIABLE_NON_VOLATILE |
             EFI_VARIABLE_BOOTSERVICE_ACCESS |
             EFI_VARIABLE_RUNTIME_ACCESS;
Status = gRT->GetVariable (
                L"ExampleConfiguration",                // VariableName
                &gEfiExampleConfigurationVariableGuid,  // VendorGuid
                &Attributes,                            // Attributes
                &DataSize,                              // DataSize
                &ExampleConfiguration                   // Data
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

The code fragment below is identical in functionality to the previous example,
but uses the `GetVariable()` function from the EDK II library `UefiLib` to read
the configuration structure from the UEFI variable. The UEFI variable contents
are allocated from pool, so the variable contents must be freed after they are
used. The `UefiLib` function `GetVariable()` supports reading both fixed size
UEFI variables such as an `EXAMPLE_CONFIGURATION` structure and UEFI variables
whose size may vary.

###### Example 67-Use UefiLib to read configuration structure from a UEFI variable

```c
#include <Uefi.h>
#include <Library/UefiLib.h>
#include <Guid/ExampleConfigurationVariable.h>

EXAMPLE_CONFIGURATION *ExampleConfiguration;

ExampleConfiguration = GetVariable (
                         L"ExampleConfiguration",
                         &gEfiExampleConfigurationVariableGuid
                         );
if (ExampleConfiguration == NULL) {
  return EFI_NOT_FOUND;
}

//
// When done, free the UEFI variable contents
//
FreePool (ExampleConfiguration);
```
