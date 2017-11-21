<!--- @file
  A.2.1 _ComponentName.c File_

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

### A.2.1 _ComponentName.c File_

###### Example A-6-Component Name Protocol implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include "<<DriverName>>.h"

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME_PROTOCOL g<<DriverName>>ComponentName = {
  (EFI_COMPONENT_NAME_GET_DRIVER_NAME) <<DriverName>>ComponentNameGetDriverName,
  (EFI_COMPONENT_NAME_GET_CONTROLLER_NAME) <<DriverName>>ComponentNameGetControllerName,
  "<<Iso639SupportedLanguages>>"
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME2_PROTOCOL g<<DriverName>>ComponentName2 = {
  <<DriverName>>ComponentNameGetDriverName,
  <<DriverName>>ComponentNameGetControllerName,
  "<<Rfc4646SupportedLanguages>>"
};

EFI_STATUS
EFIAPI
<<DriverName>>ComponentNameGetDriverName (
  IN  EFI_COMPONENT_NAME2_PROTOCOL  *This,
  IN  CHAR8                         *Language,
  OUT CHAR16                        **DriverName
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>ComponentNameGetControllerName (
  IN  EFI_COMPONENT_NAME2_PROTOCOL  *This,
  IN  EFI_HANDLE                    ControllerHandle,
  IN  EFI_HANDLE                    ChildHandle,  OPTIONAL
  IN  CHAR8                         *Language,
  OUT CHAR16                        **ControllerName
  )
{
}
```
