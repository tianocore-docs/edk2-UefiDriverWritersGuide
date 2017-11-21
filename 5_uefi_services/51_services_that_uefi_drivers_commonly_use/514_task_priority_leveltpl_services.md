<!--- @file
  5.1.4 Task Priority Level(TPL) Services

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

### 5.1.4 Task Priority Level(TPL) Services

The Task Priority Level Services provide a mechanism for code to execute code
at a raised priority for short periods of time. One use case is a UEFI Driver
that is required to raise the priority because the implementation of a service
of a specific protocol requires execution at a specific TPL to be UEFI
conformant. Another use case is a UEFI Driver that needs to implement a simple
lock, or critical section, on global data structures maintained by the UEFI
Driver. Event notification functions, covered in the next section, always
execute at raised priority levels.

The service `RaiseTPL()` is used to raise the priority level from its current
level to a higher level and return the priority level before it was raised. The
service `RestoreTPL()` is used to restore a the priority level to a priority
level returned by `RaiseTPL()`. These two services are always used in pairs.

**********
**Note:** _There are no UEFI services provided to lower the TPL, and it is
illegal to use `RaiseTPL()` to attempt to raise the priority level to a level
below the current priority level. If attempted, the behavior of the platform is
indeterminate._
**********

The Event, Timer, and Task Priority Services section of the _UEFI
Specification_ defines four TPL levels. These are `TPL_APPLICATION`,
`TPL_CALLBACK`, `TPL_NOTIFY`, and `TPL_HIGH_LEVEL`. UEFI Driver and UEFI Applications are started at `TPL_APPLICATION`. UEFI Drivers should execute code at the lowest possible TPL level and minimize the time spent at raised TPL levels.

**********
**Note:** _Only `TPL_APPLICATION`, `TPL_CALLBACK`, `TPL_NOTIFY`, and
`TPL_HIGH_LEVEL` may be used by UEFI Drivers. All other values are reserved
for use by the firmware. Using them results in unpredictable behavior. Good
coding practice dictates that all code should execute at its lowest possible
TPL level, and the use of TPL levels above `TPL_APPLICATION` must be
minimized. Executing at TPL levels above `TPL_APPLICATION` for extended
periods of time may also result in unpredictable behavior._
**********

UEFI firmware, applications, and drivers all run on one thread on one
processor. However, `UEFI firmware does` `support a single timer interrupt`.
Because UEFI code can run in interrupt context, it is possible that a global
data structure can be accessed from both normal context and interrupt context.
As a result, global data structures that are accessed from both normal context
and interrupt context must be protected by a lock.

The following code fragment shows how the `RaiseTPL()` and `RestoreTPL()`
services can be used to implement a lock when the contents of a global variable
are modified. The timer interrupt is blocked at `EFI_TPL_HIGH_LEVEL`, so most
locks raise to this level.

###### Example 45-Using TPL Services for a Global Lock

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

UINT32 gCounter;
EFI_TPL OldTpl;

//
// Raise the Task Priority Level to TPL_HIGH_LEVEL to block timer
// interrupts
//
OldTpl = gBS->RaiseTPL (TPL_HIGH_LEVEL);

//
// Increment the global variable now that it is safe to do so.
// gCounter++;
//
// Restore the Task Priority Level to its original level
//
gBS->RestoreTPL (OldTpl);
```

The code fragment in Example 46, below, has the same functionality as _Example
45_, above, but uses the lock macros and functions from the EDK II Library
`UefiLib` that use `RaiseTPL()` and `RestoreTPL()` to implement general purpose
locks. The global variable `gLock` is an `EFI_LOCK` structure that is
initialized using the `EFI_INITIALIZE_LOCK_VARIABLE()` macro that specifies the use of
`TPL_HIGH_LEVEL` when the lock is acquired. The `EfiAcquireLock()` and
`EfiReleaseLock()` functions hide the details of managing TPL levels.

###### Example 46-Using UEFI Library for a Global Lock

```c
#include <Uefi.h>
#include <Library/UefiLib.h>

EFI_LOCK gLock = EFI_INITIALIZE_LOCK_VARIABLE (TPL_HIGH_LEVEL);
UINT32 gCounter;

//
// Acquire the lock to block timer interrupts
//
EfiAcquireLock (&gLock);

//
// Increment the global variable now that it is safe to do so.
// gCounter++;
//
// Release the lock
//
EfiReleaseLock (&gLock);
```

The algorithm shown in these two global lock examples also applies to a UEFI
Driver that is required to implement protocol services that execute at a
specific TPL level. For example, the services in the Block I/O Protocol must be
called at or below `TPL_CALLBACK`. This means that the implementation of the `ReadBlocks()`,
`WriteBlocks()`, and `FlushBlocks()` services should raise the priority level
to `TPL_CALLBACK`. This would be identical to Example 46, above, but would use
`TPL_CALLBACK` instead of `TPL_HIGH_LEVEL`.
