<!--- @file
  11.2 GetDriverName() Implementations

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

## 11.2 GetDriverName() Implementations

The `GetDriverName()` service retrieves the name of a UEFI Driver. It may be
used to retrieve the name of a UEFI Driver even if the UEFI Driver is not
managing any devices. Example 129, below, shows a typical implementation of the
`GetDriverName()` service for the Component Name 2 Protocol along with a table
of Unicode strings for the UEFI Driver name in English, French, and Spanish.
The recommended implementation style shown here allows the same
`GetDriverName()` service implementation to be shared between the Component
Name Protocol and the Component Name 2 Protocol. The `UefiLib` function
`LookupUnicodeString2()` supports looking up strings using either ISO 639-2 or
RFC 4646 language code formats.

The static table of driver names contains two elements per entry. The first is
an ASCII string containing one or more language codes separated by ';'
characters. The language codes may be in the ISO639-2 or the RFC 4646 format.

The second element is a Unicode string representing the name of the UEFI Driver
for the set of languages specified by the first element. The static table is
terminated by two `NULL` elements. The format is very size efficient because
each Unicode string name for the UEFI Driver can be associated with many
language codes.

###### Example 129-GetDriverName() for Device, Bus, or Hybrid Driver

```c
#include <Uefi.h>
#include <Protocol/ComponentName2.h>
#include <Library/UefiLib.h>

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_UNICODE_STRING_TABLE mAbcDriverNameTable[] = {
  { "eng;en", (CHAR16 *)L"ABC Driver in English"},
  { "fra;fr", (CHAR16 *)L"ABC Driver in French"},
  { "spa;sp", (CHAR16 *)L"ABC Driver in Spanish"},
  { NULL, NULL }
};

EFI_STATUS
EFIAPI
AbcGetDriverName (
  IN  EFI_COMPONENT_NAME2_PROTOCOL  *This,
  IN  CHAR8                         *Language,
  OUT CHAR16                        **DriverName
  )
{
  return LookupUnicodeString2 (
           Language,
           This->SupportedLanguages,
           mAbcDriverNameTable,
           DriverName,
           (BOOLEAN)(This != &gAbcComponentName2)
           );
}
```
