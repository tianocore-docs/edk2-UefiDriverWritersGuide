<!--- @file
  11.1 Component Name Protocol Implementations

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

## 11.1 Component Name Protocol Implementations

The implementation of the Component Name Protocols for a specific driver is
typically found in the file ComponentName.c. Appendix A contains a template for
a ComponentName.c file for a UEFI Driver. This file typically contains the
following:
* Add global variable for the `EFI_COMPONENT_NAME_PROTOCOL` instance to
  `ComponentName.c`. - Add global variable for the
  `EFI_COMPONENT_NAME2_PROTOCOL` instance to `ComponentName.c`.
* `EFI_COMPONENT_NAME2_PROTOCOL` instance
* Add static table of UEFI Driver names as Unicode strings to `ComponentName.c`.
* Add static table of controller names as Unicode strings to `ComponentName.c`.
* Implementation of the `GetDriverName()` service
* Implementation of the `GetControllerName()` service
* Install all the Component Name Protocols in the driver entry point.
* If the UEFI Driver supports the unload feature, uninstall all the Component
  Name Protocols in the `Unload()` function.

The Component Name Protocols provide names in one or more languages. At a
minimum, the protocols should support the English language. The Component Name
Protocols advertise the languages they supports in a data field called
_SupportedLanguages_. This data filed is a null-terminated ASCII string that
contains one or more 3 character ISO 639-2 language codes with no separator
character. The Component Name 2 Protocol also advertises the languages it
supports in a data field called _SupportedLanguages_. This data filed is a
null-terminated ASCII string that contains one or more RFC 4646 language codes
separated by semicolons (';').

A consumer of the Component Name Protocols may parse the _SupportedLanguages_
data field to determine if the protocol supports a language in which the
consumer is interested. This data field can also be used by the implementation
of the Component Name Protocols to see if names are available in the requested
language.

For reference, _Example 126_, below, shows the protocol interface structure for
the Component Name Protocol and _Example 127_ shows the protocol interface
structure for the Component Name 2 Protocol. Both are composed of the two
services called `GetDriverName()` and `GetControllerName()` and a data field called
`SupportedLanguages`

###### Example 126-Component Name Protocol

```c
typedef struct _EFI_COMPONENT_NAME_PROTOCOL EFI_COMPONENT_NAME_PROTOCOL;
///
/// This protocol is used to retrieve user readable names of drivers
/// and controllers managed by UEFI Drivers.
///
struct _EFI_COMPONENT_NAME_PROTOCOL {
  EFI_COMPONENT_NAME_GET_DRIVER_NAME GetDriverName;
  EFI_COMPONENT_NAME_GET_CONTROLLER_NAME GetControllerName;
  ///
  /// A Null-terminated ASCII string that contains one or more
  /// ISO 639-2 language codes. This is the list of language codes
  /// that this protocol supports.
  ///
  CHAR8 *SupportedLanguages;
};
```

###### Example 127-Component Name 2 Protocol

```c
typedef struct _EFI_COMPONENT_NAME2_PROTOCOL  
  EFI_COMPONENT_NAME2_PROTOCOL;

///
/// This protocol is used to retrieve user readable names of drivers
/// and controllers managed by UEFI Drivers.
///
struct _EFI_COMPONENT_NAME2_PROTOCOL {
  EFI_COMPONENT_NAME2_GET_DRIVER_NAME GetDriverName;
  EFI_COMPONENT_NAME2_GET_CONTROLLER_NAME GetControllerName;
  ///
  /// A Null-terminated ASCII string array that contains one or more
  /// supported language codes. This is the list of language codes that
  /// this protocol supports. The number of languages supported by a
  /// driver is up to the driver writer. SupportedLanguages is
  /// specified in RFC 4646 format.
  ///
  CHAR8                                   *SupportedLanguages;
};
```

###### Example 128-Driver Diagnostics Protocol declaration

UEFI Drivers declare global variables for the Component Name Protocol and
Component Name 2 Protocol instances that are produced. The _SupportedLanguages_
fields are typically initialized by the UEFI Driver in the declaration for the
specific set of languages the UEFI Driver supports. The following _following
example_ shows how the Component Name Protocols are typically declared in a
driver and, in this case, declared to support both English and French.

```c
#include <Uefi.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/ComponentName.h>

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME_PROTOCOL gAbcComponentName = {
  (EFI_COMPONENT_NAME_GET_DRIVER_NAME) AbcGetDriverName,
  (EFI_COMPONENT_NAME_GET_CONTROLLER_NAME) AbcGetControllerName,
  "engfra"
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME2_PROTOCOL gAbcComponentName2 = {
  AbcGetDriverName,
  AbcGetControllerName,
  "en;fr"
};
```

The implementations of the Component Name Protocols change in complexity
depending on the type of UEFI Driver Model driver and the specific Component
Name Protocol features implemented. A device driver is the simplest to
implement. A bus driver or a hybrid driver may be more complex because it may
provide names for both the bus controller and the child controllers. These
implementations are discussed later in this section.

The `EFI_COMPONENT_NAME_PROTOCOL` and `EFI_COMPONENT_NAME2_PROTOCOL` are
installed onto the driver's image handle. It is possible for a driver to
produce more than one instance of the Component Name Protocols. All additional
instances of the Component Name Protocols must be installed onto new handles.

The Component Name Protocols can be installed directly using the UEFI Boot
Service `InstallMultipleProtocolInterfaces()`. However, the EDK II library
`UefiLib` provides a number of helper functions to install the Component Name
Protocols. The helper functions covered in more detail in _Chapter 7_ are:
* `EfiLibInstallDriverBinding()`
* `EfiLibInstallAllDriverProtocols()`
* `EfiLibInstallDriverBindingComponentName2()`
* `EfiLibInstallAllDriverProtocols2()`

If an error is generated installing any of the Component Name Protocol
instances the entire driver should fail and return an error status such as
`EFI_ABORTED`. If a UEFI Driver implements the `Unload()` feature, any
Component Name Protocol instances installed in the driver entry point must be
uninstalled in the `Unload()` function.

The simplest implementation of the Component Name Protocols provides the name
of the UEFI Driver. The next most complex implementation is that for a device
driver providing both the name of the UEFI Driver and the names of the
controllers under UEFI Driver management. The most complex implementation is
that of a bus or a hybrid driver producing names for the UEFI Driver, names for
the bus controllers it is managing, and names for the child controllers the
driver has produced. All three of these implementations are discussed in the
sections that follow.

The EDK II library `UefiLib` provides functions to simplify the implementation
of the Component Name Protocols. These library functions provide services to register
Unicode strings in a table, lookup Unicode strings in a table, and free tables
of Unicode strings. Some UEFI Drivers have fixed names for the UEFI Driver
itself and the controllers that they manage. Other UEFI Drivers may dynamically
create names based on information retrieved from the platform or the controller
itself. The EDK II library `UefiLib` functions managing tables of Unicode
strings are:
* `LookupUnicodeString()`
* `LookupUnicodeString2()`
* `AddUnicodeString()`
* `AddUnicodeString2()`
* `FreeUnicodeStringTable()`

UEFI Drivers producing dynamic names for controllers or children register those
dynamic names in the Driver Binding Protocol `Start()` function and are freed
in the Driver Binding `Stop()` function. In addition, dynamic name tables
require extra fields in the driver's private context data structure pointing to
the dynamic name tables. See _Chapter 8_ of this guide for details on the
design of private context data structures.
