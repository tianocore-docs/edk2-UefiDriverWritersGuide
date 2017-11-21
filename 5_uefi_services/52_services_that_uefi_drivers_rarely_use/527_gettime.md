<!--- @file
  5.2.7 GetTime()

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

### 5.2.7 `GetTime()`

This service is rarely used. Use it only when the current time and date are
required, such as marking the time and date of a critical error.

**********
**Caution:** This service is typically only accurate to about 1 second. As a
result, UEFI drivers should not use this service to poll or wait for an event
from a device. Instead, the `Stall()` service should be used for short
delays. The `CreateEvent()`, `CreateEventEx()`, and `SetTimer()` services
should be used for longer delays.
**********

_Example 69_ and _Example 70_, following, are two examples of the `GetTime()`
service. The first retrieves the current time and date in an `EFI_TIME`
structure. The second retrieves both the current time and date in an `EFI_TIME`
structure and the capabilities of the realtime clock hardware in an
`EFI_TIME_CAPABILITIES` structure.

###### Example 69-Get time and date

```c
#include <Uefi.h>
#include <Library/UefiRuntimeServicesTableLib.h>

EFI_STATUS Status;
EFI_TIME Time;

Status = gRT->GetTime (&Time, NULL);
```

###### Example 70-Get real time clock capabilities

```c
#include <Uefi.h>
#include <Library/UefiRuntimeServicesTableLib.h>

EFI_STATUS Status;
EFI_TIME Time;
EFI_TIME_CAPABILITIES Capabilities;

Status = gRT->GetTime (&Time, &Capabilities);
```

