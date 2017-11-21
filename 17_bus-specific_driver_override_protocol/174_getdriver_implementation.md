<!--- @file
  17.4 GetDriver() Implementation

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

## 17.4 GetDriver() Implementation

The example below shows an example implementation of the `GetDriver()` function
of the Bus Specific Driver Override Protocol. The first step is to retrieve the
private context structure from the _This_ pointer using the `CR()` macro
defined in Section 17.3 above. If no image handles are registered,
`EFI_NOT_FOUND`. is returned. If _DriverImageHandle_ is a pointer to `NULL`, the first image handle from
_HandleBuffer_ is returned. If _DriverImageHandle_ is not a pointer to `NULL`,
a search is made through _HandleBuffer_ to find a matching handle. If a
matching handle is not found, `EFI_INVALID_PARAMETER` is returned. If a
matching handle _is_ found, the next handle in the array is returned. If the
matching handle is the last handle in the array, `EFI_NOT_FOUND` is returned.

###### Example 167-GetDriver() Function of a Bus Specific Driver Override Protocol

```c
#include <Uefi.h>
#include <Protocol/BusSpecificDriverOverride.h>

EFI_STATUS
EFIAPI
AbcGetDriver (
  IN     EFI_BUS_SPECIFIC_DRIVER_OVERRIDE_PROTOCOL  *This,
  IN OUT EFI_HANDLE                                 *DriverImageHandle
  )
{
  UINTN                                             Index;
  ABC_PRIVATE_DATA                                  *Private;
  
  Private = ABC_PRIVATE_DATA_FROM_BUS_SPECIFIC_DRIVER_OVERRIDE_THIS (This);
  
  if (Private->NumberOfHandles == 0) {
    return EFI_NOT_FOUND;
  }
  
  if (DriverImageHandle == NULL) {
    return EFI_INVALID_PARAMETER;
  }
  
  if (*DriverImageHandle == NULL) {
    *DriverImageHandle = Private->HandleBuffer[0];
    return EFI_SUCCESS;
  }
  
  for (Index = 0;
       Index < Private->NumberOfHandles;
       Index++) {
    if (*DriverImageHandle == Private->HandleBuffer[Index]) {
      Index++;
      if (Index < Private->NumberOfHandles) {
        *DriverImageHandle = Private->HandleBuffer[Index];
        return EFI_SUCCESS;
      } else {
        return EFI_NOT_FOUND;
      }
    }
  }
  
  return EFI_INVALID_PARAMETER;
}
```
