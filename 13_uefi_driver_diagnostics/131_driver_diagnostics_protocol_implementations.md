<!--- @file
  13.1 Driver Diagnostics Protocol Implementations

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

## 13.1 Driver Diagnostics Protocol Implementations

**The implementation of the Driver Diagnostics Protocols for a specific driver
is typically found in the file DriverDiagnostics.c. Appendix A contains a
template for**

DriverDiagnostics.c, a file for a UEFI Driver. This file typically contains
the following: - Add global variable for the EFI_DRIVER_DIAGNOSTICS_PROTOCOL
instance to DriverDiagnostics.c. - Add global variable for the
EFI_DRIVER_DIAGNOSTICS2_PROTOCOL instance to DriverDiagnostics.c.

* Add Static table of diagnostics result messages as Unicode strings to
  `DriverDiagnostics.c`.
* Implementation of the `RunDiagnostics()` service
* Install all the Driver Diagnostics Protocols in the driver entry point.
* If the UEFI Driver supports the unload feature, uninstall all the Driver
  Diagnostics Protocols in the `Unload()` function.

The Driver Diagnostics Protocols provide diagnostics result messages in one or
more languages. At a minimum, the protocols should support the English
language. The Driver Diagnostic Protocol advertises the languages it supports
in a data field called _SupportedLanguages_. This data field is a
null-terminated ASCII string that contains one or more 3 character ISO 639-2
language codes with no separator character. The Driver Diagnostic 2 Protocol
also advertises the languages it supports in a data field called
_SupportedLanguages_. This data filed is a null-terminated ASCII string that
contains one or more RFC 4646 language codes separated by semicolons (';').

A consumer of the Driver Diagnostics Protocols may parse the
_SupportedLanguages_ data field to determine if the protocol supports a
language in which the consumer is interested. This data field can also be used
by the implementation of the Driver Diagnostics Protocols to see if diagnostics
result messages are available in the requested language.

_Example 144_, below, shows the protocol interface structure for the Driver
Diagnostic Protocol and the following _Example 145_ shows the protocol interface structure for the Driver Diagnostics 2 Protocol for reference. Both are composed of one service called `RunDiagnostics()` and a data field called `SupportedLanguages`.

###### Example 144-Driver Diagnostics Protocol

```c
typedef struct _EFI_DRIVER_DIAGNOSTICS_PROTOCOL EFI_DRIVER_DIAGNOSTICS_PROTOCOL;

///
/// Used to perform diagnostics on a controller that an EFI Driver is managing.
///
struct _EFI_DRIVER_DIAGNOSTICS_PROTOCOL {
  EFI_DRIVER_DIAGNOSTICS_RUN_DIAGNOSTICS RunDiagnostics;
  ///
  /// A Null-terminated ASCII string that contains one or more ISO 639-2
  /// language codes. This is the list of language codes that this protocol
  /// supports.
  ///
  CHAR8 *SupportedLanguages;
};
```

###### Example 145-Driver Diagnostics 2 Protocol

```c
typedef struct _EFI_DRIVER_DIAGNOSTICS2_PROTOCOL EFI_DRIVER_DIAGNOSTICS2_PROTOCOL;

///
/// Used to perform diagnostics on a controller that an EFI Driver is managing.
///
struct _EFI_DRIVER_DIAGNOSTICS2_PROTOCOL {
  EFI_DRIVER_DIAGNOSTICS2_RUN_DIAGNOSTICS RunDiagnostics;
  ///
  /// A Null-terminated ASCII string that contains one or more RFC 4646
  /// language codes. This is the list of language codes that this protocol
  /// supports.
  ///
  CHAR8 *SupportedLanguages;
};
```

UEFI Drivers declare global variables for the Driver Diagnostics Protocol and
Driver Diagnostics 2 Protocol instances produced. The _SupportedLanguages_
fields are typically initialized by the UEFI Driver in the declaration for the
specific set of language the UEFI Driver supports. The example below shows how
the Driver Diagnostics Protocols are typically declared in a driver, and in
this case declared to support both English and French.

###### Example 146-Driver Diagnostics Protocol declaration

```c
#include <Uefi.h>
#include <Protocol/DriverDiagnostics.h>
#include <Protocol/DriverDiagnostics2.h>

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_DRIVER_DIAGNOSTICS_PROTOCOL gAbcDriverDiagnostics = {
  (EFI_DRIVER_DIAGNOSTICS_RUN_DIAGNOSTICS) AbcRunDiagnostics,
  "engfra"
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_DRIVER_DIAGNOSTICS2_PROTOCOL gAbcDriverDiagnostics2 = {
  AbcRunDiagnostics,
  "en;fr"
};
```

The implementations of the Driver Diagnostics Protocol change in complexity
depending on the type of UEFI Driver Model driver. A device driver is the
simplest to implement. A bus driver or a hybrid driver may be more complex
because it may provide diagnostics for both the bus controller and the child
controllers. These implementations are discussed later in this chapter.

The `EFI_DRIVER_DIAGNOSTICS_PROTOCOL` and `EFI_DRIVER_DIAGNOSTICS2_PROTOCOL`
are installed onto the driver's image handle. It is possible for a driver to
produce more than one instance of the Driver Diagnostics Protocols. All
additional instances of the Driver Diagnostics Protocols must be installed onto
new handles.

The Driver Diagnostics Protocols can either be installed directly using the
UEFI Boot Service `InstallMultipleProtocolInterfaces()`. However, the EDK II
library `UefiLib` provides a number of helper functions to install the Driver
Diagnostics Protocols. The helper functions that are covered in more detail in
_Chapter 7_ are:
* `EfiLibInstallAllDriverProtocols()`
* `EfiLibInstallAllDriverProtocols2()`

If an error is generated installing any of the Driver Diagnostics Protocol
instances, then the entire driver should fail and return a error status such as
`EFI_ABORTED`. If a UEFI Driver implements the `Unload()` feature, any Driver
Diagnostics Protocol instances installed in the driver entry point must be
uninstalled in the `Unload()` function.

The implementation of the Driver Diagnostics Protocols for a specific driver
is typically found in the file DriverDiagnostics.c. This file contains the
instances of the _EFI_DRIVER_DIAGNOSTICS_PROTOCOL_ and _EFI_DRIVER_DIAGNOSTICS2_PROTOCOL_ along with the implementation of RunDiagnostics(). Appendix A contains a template for
a DriverDiagnostics.c file for a UEFI Driver.
