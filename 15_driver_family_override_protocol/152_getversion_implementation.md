<!--- @file
  15.2 GetVersion() Implementation

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

## 15.2 GetVersion() Implementation

The example below shows an example implementation of the `GetVersion()`
function of the Driver Family Override Protocol. This function returns a
`UNIT32` value and, in this case, returns a value from a define statement in
the UEFI Driver. The manufacturer of a family of controllers is free to select
any version value assignment as long as UEFI Drivers that are required to be
used over previously released UEFI Drivers have higher values.

###### Example 159-GetVersion() Function of the Driver Family Override Protocol

```c
#include <Uefi.h>
#include <Protocol/DriverFamilyOverride.h>

#define ABC_DRIVER_FAMILY_OVERRIDE_VERSION 0x00050063

UINT32
EFIAPI
AbcGetVersion (
  IN EFI_DRIVER_FAMILY_OVERRIDE_PROTOCOL  *This
  )
{
  return ABC_DRIVER_FAMILY_OVERRIDE_VERSION;
}
```
