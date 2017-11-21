<!--- @file
  A.1 _UEFI Driver Template_

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

## A.1 _UEFI Driver Template_

UEFI driver sources are typically placed in a EDK II package. There are no
restrictions on the directory structure within an EDK II package. A common
convention for UEFI Drivers related to industry standard busses is to place the
UEFI Driver in a directory path the such as
`/<<PackageName>>/Bus/<<BUSTYPE>>/<<DriverName>>`. The directory structure for
a single UEFI Driver does not have to be flat. Multiple closely related UEFI
Drivers may be placed in subdirectories. The directory name for a UEFI driver
is typically of the form `<<DriverName>>`. For example, the USB keyboard driver
in the `MdeModulePkg` is located in the directory `\MdeModulePkg\Bus\Usb\UsbKb`.

Simple UEFI drivers typically have the following three files in their driver
directory:

* `<<DriverName>>.inf`

* `<<DriverName>>.h`

* `<<DriverName>>.c`

It is possible to reduce the number of files down to just
`<<DriverName>>`.inf and `<<DriverName>>`.c. However, if the complexity of the
UEFI Driver increases over time where a splitting out a second .c file makes
sense, then a common .h file is usually required. If a UEFI Driver is
implemented with a common .h file from the beginning, then additional .c file
can be added without have to reorganize the other source files. The
`<<DriverName>>`.inf file describes the information the EDK II build system
required to build UEFI Driver into a UEFI conformant executable image. This
includes elements such as source filenames, EDK II package dependencies,
libraries that are used, Protocols that are produced/consumed, and GUIDs that
are used.

The `<<DriverName>>`.h file includes the standard UEFI include file, include
files for libraries that the UEFI Driver uses, and include files for protocols
or GUIDs that the UEFI Driver either produces or consumes. In addition, the
`<<DriverName>>`.h file may contain the function prototypes for the public APIs
that are produced by the UEFI Driver and declarations for #defines and data
structures that are internal to the UEFI Driver implementation.

The `<<DriverName>>`.c file contains the driver entry point. If a UEFI driver
produces the Driver Binding Protocol, then the `<<DriverName>>`.c file
typically contains the Supported(), Start(), and Stop() services. The
`<<DriverName>>`.c file may also contain the services for other protocol(s)
that the UEFI driver produces.

Complex UEFI drivers that produce more than one protocol may be broken up
into multiple source files. The natural organization is to place the
implementation of each protocol that is produced in a separate file of the form
`<<ProtocolName>>.c` or `<<DriverName>><<ProtocolName>>.c`. For example, the disk I/O driver
produces the Driver Binding Protocol, the Disk I/O Protocol, the Component Name Protocol, and the Component Name2 Protocol. The `DiskIo.c` file contains the Driver Binding
Protocol and Disk I/O Protocol implementations. The `ComponentName.c` file
contains the implementation of the Component Name Protocol and the Component
Name2 Protocol.

### A.1.1 `<<DriverName>>.inf` File for a UEFI Driver

A UEFI Driver module information file typically consists of the following
elements. The following example shows a template of an INF file with these
same elements.

* [Defines] section that declares a name for GUID for the UEFI Driver along
  with the name of the function that is the entry point to the UEFI Driver.

* [Sources] section with the list of .c and .h files required to build the UEFI
  Driver.

* [Packages] section with the list of EDK II packages that the UEFI Driver
  requires to build. All UEFI Drivers use MdePkg/MdePkg.dec for the definitions
  from the _UEFI Specification_. If a UEFI Driver implementation uses Protocols
  or GUIDs declared in other EDK II Packages, then those packages must be
  listed in this section too.

* [LibraryClasses] section with the list of libraries that the UEFI Driver uses.

* [Protocols] section with the list of protocols that the UEFI Driver produces
  or consumes.

* [Guids] section with the list of GUIDs that the UEFI Driver produces or
  consumes.

###### Example A-1-UEFI Driver INF file template

```ini
## @file
# <<BriefDescription>>
#
# <<DetailedDescription>>
#
# <<Copyright>>
#
# <<License>>
#
##

[Defines]
  INF_VERSION    = 0x00010005
  BASE_NAME      = <<DriverName>>
  FILE_GUID      = <<GUID_REGISTRY_FORMAT>>
  MODULE_TYPE    = UEFI_DRIVER
  VERSION_STRING = <<DriverVersionString>>
  ENTRY_POINT    = <<DriverName>>DriverEntryPoint

[Sources]
  <<DriverName>>.h
  <<DriverName>>.c

[Packages]
  MdePkg/MdePkg.dec
  #
  # List other packages that the UEFI Driver depends upon
  #
  <<PackageName>>/<<PackageName>>.dec

[LibraryClasses]
  UefiDriverEntryPoint
  UefiBootServicesTableLib
  MemoryAllocationLib
  BaseMemoryLib
  BaseLib
  UefiLib
  DevicePathLib
  DebugLib
  #
  # List of additional libraries that the UEFI Driver uses
  #

[Protocols]
  #
  # List of Protocols the UEFI Driver produces or consumes
  #
  gEfi<<ProtocolName>>ProtocolGuid

[Guids]
  #
  # List of GUIDs the UEFI Driver produces or consumes
  #
  gEfi<<GuidName>>Guid
```

### A.1.2 `<<DriverName>>.inf` File for a UEFI Runtime Driver

The requirements for the module information file for a UEFI Runtime Driver are
slightly different than UEFI Drivers. The `MODULE_TYPE` must be set to
`DXE_RUNTIME_DRIVER` and the INF file must include a fixed `[Depex]` section.
All other requirements are the same. The example below shows a template of an
INF file for a UEFI Runtime Driver and also adds the
`UefiRuntimeServicesTableLib` and `UefiRuntimeLib` to the `[LibraryClasses]`
section because those two library classes are commonly used by UEFI Runtime
Drivers.

###### Example A-2-UEFI Runtime Driver INF file template

```ini
## @file
# <<BriefDescription>>
#
# <<DetailedDescription>>
#
# <<Copyright>>
#
# <<License>>
#
##

[Defines]
  INF_VERSION    = 0x00010005
  BASE_NAME      = <<DriverName>>
  FILE_GUID      = <<GUID_REGISTRY_FORMAT>>
  MODULE_TYPE    = DXE_RUNTIME_DRIVER
  VERSION_STRING = <<DriverVersionString>>
  ENTRY_POINT    = <<DriverName>>DriverEntryPoint

[Sources]
  <<DriverName>>.h
  <<DriverName>>.c

[Packages]
  MdePkg/MdePkg.dec
  #
  # List other packages that the UEFI Driver depends upon
  #
  <<PackageName>>/<<PackageName>>.dec

[LibraryClasses]
  UefiDriverEntryPoint
  UefiBootServicesTableLib
  MemoryAllocationLib
  BaseMemoryLib
  BaseLib
  UefiLib
  DevicePathLib
  DebugLib
  UefiRuntimeServicesTableLib
  UefiRuntimeLib
  #
  # List of additional libraries that the UEFI Driver uses
  #

[Protocols]
  #
  # List of Protocols the UEFI Driver produces or consumes
  #
  gEfi<<ProtocolName>>ProtocolGuid

[Guids]
  #
  # List of GUIDs the UEFI Driver produces or consumes
  #
  gEfi<<GuidName>>Guid

[Depex]
  gEfiBdsArchProtocolGuid               AND 
  gEfiCpuArchProtocolGuid               AND 
  gEfiMetronomeArchProtocolGuid         AND
  gEfiMonotonicCounterArchProtocolGuid  AND 
  gEfiRealTimeClockArchProtocolGuid     AND 
  gEfiResetArchProtocolGuid             AND 
  gEfiRuntimeArchProtocolGuid           AND 
  gEfiSecurityArchProtocolGuid          AND 
  gEfiTimerArchProtocolGuid             AND 
  gEfiVariableWriteArchProtocolGuid     AND 
  gEfiVariableArchProtocolGuid          AND 
  gEfiWatchdogTimerArchProtocolGuid
```

### A.1.3 `<<DriverName>>.h` File

A UEFI driver include file contains the following:

* `#ifndef` / `#define` for the driver include file

* `#include` statements for the standard UEFI and UEFI driver library include
  files.

* `#include` statements for all the protocols and GUIDs that are consumed by
  the driver.

* `#include` statements for all the protocols and GUIDs that are produced by
  the driver.

* `#define` for a unique signature that is used in the private context data
  structure (see _Chapter 8_).

* `typedef struct` for the private context data structure (see _Chapter 8_).

* `#define` statements to retrieve the private context data structure from each
  protocol that is produced (see _Chapter 8_).

* `extern` statements for the global variables that the driver produces.

* Function prototype for the driver's entry point.

* Function prototypes for all of the APIs in the produced protocols

* `#endif` statement for the driver include file

This example shows a template for a UEFI Driver include file.

###### Example A-3-UEFI Driver include file template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#ifndef __EFI_<<DRIVER_NAME>>_H__
#define __EFI_<<DRIVER_NAME>>_H__

#include <Uefi.h>

//
// Include Protocols that are consumed
//
#include <Protocol/<<ProtocolNameC1>>.h>
#include <Protocol/<<ProtocolNameC2>>.h>
// . .
#include <Protocol/<<ProtocolNameCn>>.h>

//
// Include Protocols that are produced
//
#include <Protocol/<<ProtocolNameP1>>.h>
#include <Protocol/<<ProtocolNameP2>>.h>
// . .
#include <Protocol/<<ProtocolNamePm>>.h>

//
// Include GUIDs that are consumed
//
#include <Guid/<<GuidName1>>.h>
#include <Guid/<<GuidName2>>.h>
// . .
#include <Guid/<<GuidNamep>>.h>

//
// Include Library Classes commonly used by UEFI Drivers
//
#include <Library/UefiBootServicesTableLib.h>
#include <Library/MemoryAllocationLib.h>
#include <Library/BaseMemoryLib.h>
#include <Library/BaseLib.h>
#include <Library/UefiLib.h>
#include <Library/DevicePathLib.h>
#include <Library/DebugLib.h>

//
// Include additional Library Classes that are used
//
#include <Library/<<LibraryName1>>.h>
#include <Library/<<LibraryName2>>.h>
// . .
#include <Library/<<LibraryNameq>>.h>

//
// Define driver version Driver Binding Protocol
//
#define <<DRIVER_NAME>_VERSION>> <<DriverVersion>>

//
// Private Context Data Structure
//
#define <<DRIVER_NAME>>_PRIVATE_DATA_SIGNATURE SIGNATURE_32 ('A','B','C','D')

typedef struct {
  UINTN Signature;
  EFI_HANDLE Handle;
  
  //
  // Pointers to consumed protocols
  //
  EFI_ <<PROTOCOL_NAME_C1>>_PROTOCOL * <<ProtocolNameC1>>;
  EFI_ <<PROTOCOL_NAME_C2>>_PROTOCOL * <<ProtocolNameC2>>;
  // . .
  EFI_ <<PROTOCOL_NAME_Cn>>_PROTOCOL * <<ProtocolNameCn>>;
  
  //
  // Produced protocols
  //
  EFI_ <<PROTOCOL_NAME_P1>>_PROTOCOL <<ProtocolNameP1>>;
  EFI_ <<PROTOCOL_NAME_P2>>_PROTOCOL <<ProtocolNameP2>>;
  // . .
  EFI_ <<PROTOCOL_NAME_Pm>>_PROTOCOL <<ProtocolNamePm>>;
  
  //
  // Private functions and data fields
  //
} <<DRIVER_NAME>>_PRIVATE_DATA;

#define <<DRIVER_NAME>_PRIVATE_DATA_FROM_<<PROTOCOL_NAME_P1>>_THIS(a) \
  CR(                                      \
    a,                                     \
    <<DRIVER_NAME>>_PRIVATE_DATA,          \
    <<ProtocolNameP1>>,                    \
    <<DRIVER_NAME>>_PRIVATE_DATA_SIGNATURE \
    )

#define <<DRIVER_NAME>_PRIVATE_DATA_FROM_<<PROTOCOL_NAME_P2>>_THIS(a) \
  CR(                                      \
    a,                                     \
    <<DRIVER_NAME>>_PRIVATE_DATA,          \
    <<ProtocolNameP2>>,                    \
    <<DRIVER_NAME>>_PRIVATE_DATA_SIGNATURE \
    )

// . .

#define <<DRIVER_NAME>_PRIVATE_DATA_FROM_<<PROTOCOL_NAME_Pm>>_THIS(a) \
  CR(                                      \
    a,                                     \
    <<DRIVER_NAME>>_PRIVATE_DATA,          \
    <<ProtocolNamePm>>,                    \
    <<DRIVER_NAME>>_PRIVATE_DATA_SIGNATURE \
    )

//
// Required Global Variables
//
extern EFI_DRIVER_BINDING_PROTOCOL g<<DriverName>>DriverBinding;

//
// Optional Global Variables depending on driver features
//
extern EFI_COMPONENT_NAME2_PROTOCOL          g<<DriverName>>ComponentName2;
extern EFI_HII_CONFIG_ACCESS_PROTOCOL        g<<DriverName>>ConfigAccess;
extern EFI_DRIVER_DIAGNOSTICS2_PROTOCOL      g<<DriverName>>DriverDiagnostics2;
extern EFI_DRIVER_FAMILY_OVERRIDE_PROTOCOL   g<<DriverName>>DriverFamilyOverride;
extern EFI_DRIVER_HEALTH_PROTOCOL            g<<DriverName>>DriverHealth;

//
// Optional Global Variables for compatibility with UEFI 2.0
//
extern EFI_DRIVER_CONFIGURATION2_PROTOCOL    g<<DriverName>>DriverConfiguration2;

//
// Optional Global Variables for compatibility with EFI 1.10
//
extern EFI_COMPONENT_NAME_PROTOCOL           g<<DriverName>>ComponentName;
extern EFI_DRIVER_CONFIGURATION_PROTOCOL     g<<DriverName>>DriverConfiguration;
extern EFI_DRIVER_DIAGNOSTICS_PROTOCOL       g<<DriverName>>DriverDiagnostics;

//
// Function ptototypes for the APIs in the Produced Protocols
//

#endif
```

### A.1.4 `<<DriverName>>.c` File

A UEFI source file contains:

* `#include` statement for `<<DriverName>>.h`.

* Global variable declarations

* The UEFI driver entry point function

* The `Supported()`, `Start()`, and `Stop()` functions

* Implementation of the APIs from the produced protocols

The following example shows a template for the main source file of a UEFI
Driver that follows the UEFI Driver Model and produces the Driver Binding
Protocol. The structure for the Driver Supported EFI Version Protocol is also
declared, but is not installed in the Driver Entry Point because that protocol
is optional. This template contains a template of an empty function from
additional protocols that the UEFI Driver may produce. The functions from the
various protocols that a UEFI driver may produce are discussed in later
sections. There are many optional UEFI Driver features that are not shown in
this specific template. Each of those optional features are discussed in
earlier chapters along with details on how to add each of those optional
features to a UEFI Driver.

###### Example A-4-UEFI Driver implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include "<<DriverName>>.h"

GLOBAL_REMOVE_IF_UNREFERENCED EFI_DRIVER_SUPPORTED_EFI_VERSION_PROTOCOL 
g<<DriverName>>DriverSupportedEfiVersion = {
  sizeof (EFI_DRIVER_SUPPORTED_EFI_VERSION_PROTOCOL),
  <<UEFI_SYSTEM_TABLE_REVISON>>
};

EFI_DRIVER_BINDING_PROTOCOL g<<DriverName>>DriverBinding = {
  <<DriverName>>DriverBindingSupported,
  <<DriverName>>DriverBindingStart,
  <<DriverName>>DriverBindingStop,
  <<DRIVER_NAME>>_VERSION,
  NULL,
  NULL
};

EFI_STATUS
EFIAPI
<<DriverName>>DriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  //
  // Install UEFI Driver Model protocol(s).
  //
  Status = EfiLibInstallDriverBindingComponentName2 (
             ImageHandle,
             SystemTable,
             &g<<DriverName>>DriverBinding,
             ImageHandle,
             &g<<DriverName>>ComponentName,
             &g<<DriverName>>ComponentName2
             );
  ASSERT_EFI_ERROR (Status);
  
  return Status;
}

EFI_STATUS
EFIAPI
<<DriverName>>DriverBindingSupported (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN EFI_DEVICE_PATH_PROTOCOL     *RemainingDevicePath   OPTIONAL
  )
{
  return EFI_UNSUPPORTED;
}

EFI_STATUS
EFIAPI
<<DriverName>>DriverBindingStart (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN EFI_DEVICE_PATH_PROTOCOL     *RemainingDevicePath   OPTIONAL
  )
{
  return EFI_UNSUPPORTED;
}

EFI_STATUS
EFIAPI
<<DriverName>>DriverBindingStop (
  IN EFI_DRIVER_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                   ControllerHandle,
  IN UINTN                        NumberOfChildren,
  IN EFI_HANDLE                   *ChildHandleBuffer   OPTIONAL
  )
{
  return EFI_UNSUPPORTED;
}

//
// Implementations of the APIs in the produced protocols
// The following template is for the mth function of the nth protocol produced
// It also shows how to retrieve the private context structure from this arg
//
EFI_STATUS
EFIAPI
<<DriverName>><<ProtocolNamePn>><<FunctionNameM>> (
  IN EFI_<<PROTOCOL_NAME_PN>>_PROTOCOL      *This,
  //
  // Additional function arguments here.
  //
  )
{
  <<DRIVER_NAME>>_PRIVATE_DATA *Private;

  //
  // Use This pointer to retrieve the private context structure
  //
  Private = <<DRIVER_NAME>>_PRIVATE_DATA_FROM_<<PROTOCOL_NAME_Pn>>_THIS (This);
}
```

### A.1.5 `<<ProtocolName>>.c` File

More complex UEFI drivers may break the implementation into several source
files. The natural boundary is to implement one protocol per file.

A UEFI Driver protocol source file contains:

* `#include` statement for `<<DriverName>>.h`.

* Global variable declaration. This declaration applies only to protocols such
  as the Component Name Protocols and Driver Diagnostics Protocols. Protocols
  that produce I/O services should never be declared as a global variable.
  Instead, they are declared in the private context structure that is
  dynamically allocated in the `Start()` function (see _Chapter 8_ of this
  guide).

* Implementation of the APIs from the produced protocols.

The template in the example below shows the main source file for a protocol
produced by a UEFI driver. This template contains empty protocol function
implementations. The remaining sections of this appendix shows template files
for all the optional UEFI Driver protocols.

###### Example A-5-UEFI Driver protocol implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include "<<DriverName>>.h"

//
// Protocol Global Variables
//
EFI_ <<PROTOCOL_NAME_PN>>_PROTOCOL g<<DriverName>><<ProtocolNamePn>> = {
  // . .
};

//
// Implementations of the APIs in the produced protocols
// The following template is for the mth function of the nth protocol produced
// It also shows how to retrieve the private context structure from the This
// parameter.
//
EFI_STATUS
EFIAPI
<<DriverName>><<ProtocolNamePn>><<FunctionName1M>> (
  IN EFI_<<PROTOCOL_NAME_PN>>_PROTOCOL      *This,
  //
  // Additional function arguments here.
  //
  )
{
  <<DRIVER_NAME>>_PRIVATE_DATA Private;
  
  //
  // Use This pointer to retrieve the private context structure
  //
  Private = <<DRIVER_NAME>_PRIVATE_DATA_FROM_<<PROTOCOL_NAME_Pn>>_THIS (This);
}
```
