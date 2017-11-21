<!--- @file
  5.2.6 QueryVariableInfo ()

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

### 5.2.6 `QueryVariableInfo()`

Use this UEFI Runtime Service to retrieve information about the container used
to store UEFI variables including their size, available space, and the maximum
size of a single UEFI variable.

In general, UEFI Drivers do not use UEFI variables, and those UEFI Drivers that
do use UEFI variables are provided with the UEFI system firmware where this
type of information is usually already known. As a result, this service is
rarely used by UEFI Drivers. It is more typically used by OS installers and OS
kernels to determine the platform storage capabilities for UEFI variables.

The following code fragment shows how the `QueryVariableInfo()` service is used
to collect information storage containers for UEFI variables that persist
across reboots and power cycles and are available in both the pre-boot
environment and by the OS.

###### Example 68-Collect information about the UEFI variable store

```c
#include <Uefi.h>
#include <Library/UefiRuntimeServicesTableLib.h>

EFI_STATUS Status;
UINT64 MaximumVariableStorageSize;
UINT64 RemainingVariableStorageSize;
UINT64 MaximumVariableSize;

Status = gRT->QueryVariableInfo (
                EFI_VARIABLE_BOOTSERVICE_ACCESS |
                EFI_VARIABLE_RUNTIME_ACCESS |
                EFI_VARIABLE_NON_VOLATILE,
                &MaximumVariableStorageSize,
                &RemainingVariableStorageSize,
                &MaximumVariableSize
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```
