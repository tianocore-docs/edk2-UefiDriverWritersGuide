<!--- @file
  5.1.6 SetTimer()

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

### 5.1.6 SetTimer()

This service programs a timer event to be signaled in the future. The time is
specified in 100 nanosecond (ns) units. UEFI supports both periodic timer
events and one-shot timer events. Use these timer events when polling for I/O
completions, detecting hot plug events, detecting timeout conditions for I/O
operations, supporting asynchronous I/O operations, etc.

_`Caution`: The units used for timer events may appear to have better accuracy
than the `Stall()` service, which has an accuracy of 1 μs, but that may not be
the case. UEFI uses a single timer interrupt to determine when to signal timer events. The
resolution of timer events is dependent on the frequency of the timer
interrupt._

UEFI system firmware uses a hardware timer interrupt to measure time. These.
These time measurements are used to determine when enough time has passed to
signal a timer event programmed with `SetTimer()`. In most systems, the timer
interrupt is generated every 10 ms to 50 ms, but the _UEFI Specification_ does
not require any specific interrupt rate. This lack of specificity means that a
periodic timer programmed with a period much smaller than 10 ms may only be
signaled every 10 ms to 50 ms. If short delays much smaller than 10 ms are
required, use the `Stall()` service.

**********
**TIP:** Timer event services are not accurate over short delays. If a short,
accurate delay, is required then the `Stall()` service should be used.
**********

The code fragment in _Example 53_ shows how to create a timer event and program
it as a periodic timer with a period of 100 ms. When the created event is
signaled every 100 ms, the notification function `TimerHandler()` is called at
`TPL_NOTIFY` with the `EXAMPLE_DEVICE` context that was registered when the
event was created. The EDK II library `UefiLib` provides macros for the timer
periods used with the `SetTimer()` services.

These macros include `EFI_TIMER_PERIOD_MICROSECONDS()`, `EFI_TIMER_PERIOD_MILLISECONDS()`, and `EFI_TIMER_PERIOD_SECONDS()`

The Private Context Structure a UEFI Driver uses to store device specific
information usually contains `EFI_EVENT` fields for the events the UEFI Driver
creates. This allows a UEFI Driver to close events when a device is stopped or
when a UEFI Driver is unloaded. In this example, the Private Context Structure
called `EXAMPLE_DEVICE` contains an `EFI_EVENT` for both a periodic and a
one-shot timer. The Private Context Structure is also typically passed in as
the `Context` parameter when an event is created. This provides the event
notification function with the device specific context required to perform the
device specific actions.

_`Caution`: Always close timer events with the UEFI Boot Service `CloseEvent()`
whenever a device is stopped or a UEFI Driver is unloaded. If not performed, a
call for an event notification no longer present in memory, or event
notification function for a device no longer available, may cause unexpected
failures._

###### Example 53-Create periodic timer event

```c
#include <Uefi.h>
#include <Library/UefiRuntimeServicesTableLib.h>
#include <Library/UefiLib.h>

typedef struct {
  UINTN Signature;
  EFI_EVENT PeriodicTimer;
  EFI_EVENT OneShotTimer;
  //
  // Other device specific fields
  //
} EXAMPLE_DEVICE;

VOID
TimerHandler (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  //
  // Perform a UEFI driver-specific operation.
  //
}

EFI_STATUS Status;
EXAMPLE_DEVICE *Device;
Status = gBS->CreateEvent (
                EVT_TIMER | EVT_NOTIFY_SIGNAL,  // Type
                TPL_NOTIFY,                     // NotifyTpl
                TimerHandler,                   // NotifyFunction
                Device,                         // NotifyContext
                &Device->PeriodicTimer          // Event
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Program the timer event to be signaled every 100 ms.
//
Status = gBS->SetTimer (
                Device->PeriodicTimer,
                TimerPeriodic,
                EFI_TIMER_PERIOD_MILLISECONDS (100)
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

The following code fragment shows how to create a one-shot timer event that is
signaled 4 seconds in the future. When the created event is signaled, the
notification function `TimerHandler()` is called at `TPL_CALLBACK` with the
`EXAMPLE_DEVICE` context that was registered when the event was created.

###### Example 54-Create one-shot timer event

```c
#include <Uefi.h>
#include <Library/UefiRuntimeServicesTableLib.h>
#include <Library/UefiLib.h>

typedef struct {
  UINTN Signature;
  EFI_EVENT PeriodicTimer;
  EFI_EVENT OneShotTimer;
  //
  // Other device specific fields
  //
} EXAMPLE_DEVICE;

VOID
TimerHandler (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  //
  // Perform a UEFI driver-specific operation.
  //
}

EFI_STATUS Status;
EXAMPLE_DEVICE *Device;
Status = gBS->CreateEvent (
                EVT_TIMER | EVT_NOTIFY_SIGNAL,  // Type
                TPL_CALLBACK,                   // NotifyTpl
                TimerHandler,                   // NotifyFunction
                Device,                         // NotifyContext
                &Device->OneShotTimer           // Event
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Program the timer event to be signaled 4 seconds from now.
//
Status = gBS->SetTimer (
                Device->OneShotTimer,
                TimerRelative,
                EFI_TIMER_PERIOD_SECONDS (4)
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

The code fragment below shows how to cancel and close the one-shot timer
created in Example 54 above. If the UEFI Driver completes an I/O operation
normally, any timer events used to detect timeout conditions must be canceled.
If the timeout condition is only used as part of device detection, the timer
event may not be required again. In those cases, the event can be both canceled
and closed.

<div style="page-break-after: always;"></div>

###### Example 55-Cancel and close one-shot timer event

```c
#include <Uefi.h>
#include <Library/UefiRuntimeServicesTableLib.h>
#include <Library/UefiLib.h>

typedef struct {
  UINTN Signature;
  EFI_EVENT PeriodicTimer;
  EFI_EVENT OneShotTimer;
  //
  // Other device specific fields
  //
} EXAMPLE_DEVICE;

EFI_STATUS Status;
EXAMPLE_DEVICE *Device;

//
// Cancel the one-shot timer event.
//
Status = gBS->SetTimer (Device->OneShotTimer, TimerCancel, 0);
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Close the one-shot timer event.
//
Status = gBS->CloseEvent (Device->OneShotTimer);
if (EFI_ERROR (Status)) {
  return Status;
}
```
