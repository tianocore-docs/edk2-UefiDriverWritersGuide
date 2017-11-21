<!--- @file
  A.5.4 _Including Protocols, GUIDs, and Library Classes_

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

### A.5.4 Including Protocols, GUIDs, and Library Classes

A UEFI Driver that produces or consumes a protocol or GUID must include the
protocol or GUID definitions using `#include` statements and also declare the
usage of those Protocols or GUIDs in the `[Protocols]` and `[Guids]` sections
of the INF file for the UEFI Driver. A UEFI Driver that uses defines,
structures, unions, or functions from a library class must include the those
definitions using an `#include` statement.

The `#include` statements use paths in EDK II packages to the .h files for the
required protocols or GUIDs or library classes. The include paths that an EDK
II package supports are declared in the [Includes] section of an EDK II
package. An EDK II package typically uses an include directory called Include
and use subdirectories called

Protocol and Guid and Library for .h files. The example below shows some
example `#include` statements for a UEFI Driver that uses protocols and GUIDs and
library classes from the `MdePkg`. The `MdePkg` contains all the protocols and
GUIDs defined in the UEFI Specification along with a number of library classes that are very
useful to UEFI Drivers. The include file Uefi.h pulls in all the standard
definitions from the UEFI Specification. This file must be included before any
other include files. This next three include statements pull in the Block I/O
Protocol, the Driver Binding Protocol, and the Component Name 2 Protocol. The
next 2 include statements pull in the definitions for the UEFI Global Variable
GUID and the GUID associated with the SMBIOS table that may be registered in
the UEFI System Table. The last set of include statements pull in the
definitions from a number of library classes that are commonly used by UEFI
Drivers.

###### Example A-32-Protocol, GUID, and Library Class include statements

```
#include <Uefi.h>

#include <Protocol/DriverBinding.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/BlockIo.h>

#include <Guid/GlobalVariable.h>
#include <Guid/Smbios.h>

#include <Library/UefiBootServicesTableLib.h>
#include <Library/MemoryAllocationLib.h>
#include <Library/BaseMemoryLib.h>
#include <Library/BaseLib.h>
#include <Library/UefiLib.h>
#include <Library/DebugLib.h>
```

Example A-33, below, shows a portion of an INF file for the same UEFI Driver
that requires the protocols and GUIDs included in Example A-32 above. A UEFI
Driver must declare the Protocols, GUIDs, and Library Classes the UEFI Driver
uses in the INF file. The comment blocks associated with each Protocol and GUID
and optional, but they describe how each Protocol and GUID is used by the UEFI
Driver. This specific example shows that this UEFI Driver produces the Driver
Binding Protocol and the Component Name Protocol, and it consumes the Block I/O
Protocol in the `Start()` function of the Driver Binding Protocol. It also
shows that UEFI Global Variable GUID is used to access the variable called
`ConIn` and that the SMBIOS Table GUID is used to lookup the SMBIOS Table in
the UEFI System Table.

###### Example A-33-Protocol and GUID INF statements

```ini
[LibraryClasses]
  UefiBootServicesTableLib
  MemoryAllocationLib
  BaseMemoryLib
  BaseLib
  UefiLib
  DebugLib

[Protocols]
  gEfiDriverBindingProtocolGuid   ## PRODUCES 
  gEfiComponentName2ProtocolGuid  ## PRODUCES 
  gEfiBlockIoProtocolGuid         ## TO_START

[Guids]
  gEfiGlobalVariableGuid          ## CONSUMES ## Variable:L"ConIn" 
  gEfiSmbiosTableGuid ## CONSUMES
```
