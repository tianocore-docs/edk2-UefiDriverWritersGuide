<!--- @file
  5.3.16 ResetSystem()

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

### 5.3.16 ResetSystem()

In general, UEFI drivers should not use this service. System resets should be
managed from the UEFI boot manager or OEM-provided utilities. The only
exceptions in the EDK II are keyboard drivers that detect the CTRL-ALT-DEL key
sequence in keyboard drivers to reset the platform.

The following code fragment shows how the UEFI Runtime Service `ResetSystem()`
is used to request a warm reset of the platform. The EDK II library
`UefiRuntimeServicesTableLib` provides a global variable for the UEFI Runtime
Services Table for the currently executing driver. In this example, the global variable
for the UEFI Runtime Services Table, `gRT`, is used to call the UEFI Runtime
Service `ResetSystem().`

###### Example 84-ResetSystem

```c
#include <Uefi.h>
#include <Library/UefiRuntimeServicesTableLib.h>
#include <Library/BaseLib.h>

//
// Perform a warm reset of the platform
//
gRT->ResetSystem (EfiResetWarm, EFI_SUCCESS, 0, NULL);

//
// Halt. ResetSystem should never return.
//
CpuDeadLoop ();
```
