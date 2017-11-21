<!--- @file
  5.2.11 GetNextMonotonicCount()

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

###### Example 82-Retrieve 64-bit monotonic counter value.

### 5.2.11 GetNextMonotonicCount()

This service provides a 64-bit monotonically increasing counter that is
guaranteed to provide a higher value each time `GetNextMonotonicCount()` is
called. This 64-bit counter is not related to any time source, so _this service
should never be used for delays, polling, or for any type of time measurement._

`GetNextHighMonotonicCount()` is related to this same 64-bit monotonic counter,
but that service is only intended to be used by operating systems after
`ExitBootServices()` is called to manage the non-volatile upper 32-bits of the
64-bit monotonic counter. A UEFI Driver should only use the UEFI Boot Service
`GetNextMonotonicCount()` because it manages all 64-bits of the monotonic
counter before `ExitBootServices()` is called.

The code fragment below show how `GetNextMonotonicCount()` can be used to
retrieve the next 64-bit value for the monotonic counter.

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS  Status;
UINT64      MonotonicCount;

Status = gBS->GetNextMonotonicCount (&MonotonicCount);
if (EFI_ERROR (Status)) {
  return Status;
}
```
