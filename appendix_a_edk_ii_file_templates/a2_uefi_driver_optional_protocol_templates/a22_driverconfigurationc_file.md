<!--- @file
  A.2.2 _DriverConfiguration.c File_

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

### A.2.2 _DriverConfiguration.c File_

###### Example A-7-Driver Configuration Protocol implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include "<<DriverName>>.h"

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_DRIVER_CONFIGURATION_PROTOCOL g<<DriverName>>DriverConfiguration = {
  (EFI_DRIVER_CONFIGURATION_SET_OPTIONS)    <<DriverName>>DriverConfigurationSetOptions,
  (EFI_DRIVER_CONFIGURATION_OPTIONS_VALID)  <<DriverName>>DriverConfigurationOptionsValid,
  (EFI_DRIVER_CONFIGURATION_FORCE_DEFAULTS) <<DriverName>>DriverConfigurationForceDefaults,
  "<<Iso639SupportedLanguages>>"
};

///
/// Driver Configuration 2 Protocol instance
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_DRIVER_CONFIGURATION2_PROTOCOL g<<DriverName>>DriverConfiguration2 = {
  <<DriverName>>DriverConfigurationSetOptions,
  <<DriverName>>DriverConfigurationOptionsValid,
  <<DriverName>>DriverConfigurationForceDefaults,
  "<<Rfc4646SupportedLanguages>>"
};

EFI_STATUS
EFIAPI
<<DriverName>>DriverConfigurationSetOptions (
  IN  EFI_DRIVER_CONFIGURATION2_PROTOCOL        *This,
  IN  EFI_HANDLE                                ControllerHandle,
  IN  EFI_HANDLE                                ChildHandle,       OPTIONAL
  IN  CHAR8                                     *Language,
  OUT EFI_DRIVER_CONFIGURATION_ACTION_REQUIRED  *ActionRequired
  )
{
  return EFI_UNSUPPORTED;
}
EFI_STATUS
EFIAPI
<<DriverName>>DriverConfigurationOptionsValid (
  IN EFI_DRIVER_CONFIGURATION2_PROTOCOL         *This,
  IN EFI_HANDLE                                 ControllerHandle,
  IN EFI_HANDLE                                 ChildHandle        OPTIONAL
  )
{
  return EFI_UNSUPPORTED;
}

EFI_STATUS
EFIAPI
<<DriverName>>DriverConfigurationForceDefaults (
  IN  EFI_DRIVER_CONFIGURATION2_PROTOCOL        *This,
  IN  EFI_HANDLE                                ControllerHandle,
  IN  EFI_HANDLE                                ChildHandle,       OPTIONAL
  IN  UINT32                                    DefaultType,
  OUT EFI_DRIVER_CONFIGURATION_ACTION_REQUIRED  *ActionRequired
  )
{
  return EFI_UNSUPPORTED;
}
```
