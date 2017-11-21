<!--- @file
  5.1.5 Event services

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

### 5.1.5 Event services

UEFI Boot Services are provided to create, manage, and close UEFI Events. UEFI
Drivers may use these event services for several features that may include the
following:

* Implementation of protocols that produce an `EFI_EVENT` to inform protocol
  consumers when input is available.

* Notification when `ExitBootServices()` is called by an OS Loader or OS Kernel
  so UEFI Drivers can place devices in a quiescent state or a state that is
  required for OS compatibility.

* Notification when `SetVirtualAddressMap()` is called by an OS Loader or OS
  Kernel so a UEFI Runtime Driver can translate physical addresses to virtual
  addresses.

* Timer events used to periodically poll for I/O completion and/or detect
  timeout conditions.

* Implementation of protocols that provide non-blocking I/O capabilities where
  notification of an I/O completion utilizes an `EFI_EVENT`.

#### 5.1.5.1 CreateEvent(), CreateEventEx(), and CloseEvent()

The `CreateEvent(), CreateEventEx(),` and `CloseEvent()` services are used to
create and close events. The following two basic types of events can be created:

* `EVT_NOTIFY_SIGNAL`

* `EVT_NOTIFY_WAIT`

The type of event determines when an event's notification function is invoked.
The notification function for signal type events is invoked when an event is
placed into the signaled state with a call to `SignalEvent()`. The notification
function for wait type events is invoked when the event is passed to the
`CheckEvent()` or `WaitForEvent()` services.

UEFI Drivers that produce protocols providing an `EFI_EVENT` field to indicate
when input is available are required to create events of type
`EVT_NOTIFY_WAIT`. Consumers of these protocols may use `CheckEvent()` or
`WaitForEvent()` to check when input is available.

Protocols from the _UEFI Specification_ containing this use case include the
Simple Text Input Protocols, the Pointer Protocols, and the Simple Network
Protocol. The complete list follows:

* `EFI_ABSOLUTE_POINTER_PROTOCOL`

* `EFI_SIMPLE_NETWORK_PROTOCOL`

* `EFI_SIMPLE_POINTER_PROTOCOL`

* `EFI_SIMPLE_TEXT_INPUT_PROTOCOL`

* `EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL`

Some UEFI drivers are required to place their controllers in a quiescent state
or perform other controller-specific actions when an operating system is about
to take full control of the platform. In this case, the UEFI driver should
create a signal type event that is notified when `ExitBootServices()` is called
by the operating system.

UEFI Runtime Drivers may need to be notified when `SetVirtualAddressMap()` is
called to convert physical addresses to virtual addresses. A complete example
for this use case, including the use of `CreateEventEx()`, is shown in _Section
5.2.9_.

UEFI Drivers may use timer events to periodically poll for device status
changes, poll for an I/O completion or detect timeouts. A complete example
showing how to create periodic and one-shot timer events using
`CreateEventEx()` is provided in _Section 5.1.6_.

**********
**Note:** _If a UEFI Driver creates events in its driver entry point, those
events must be closed with `CloseEvent()` in the UEFI Driver's `Unload()` function.
**********
**Note:** _If a UEFI Driver creates events in its Driver Binding Protocol `Start()` function associated with a device, those events must be closed
with `CloseEvent()` in its Driver Binding Protocol` Stop()` function._
**********
**Note:** _If a UEFI Driver creates events as part of an I/O operation, the
event should be closed with `CloseEvent()` when the I/O operation is
completed._
**********
**Caution:** If the `CloseEvent()` service is not used to close events
created with `CreateEvent()` or `CreateEventEx()`,the event consumes
memory and generates a memory leak._
**********

The code fragment below shows an example of a wait event created by a keyboard
driver producing the `EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL`. The first part of the
code fragment is the event notification function plus an internal worker
function that are called when the status of the wait event is checked with the
`CheckEvent()` or the `WaitForEvent()` services. The second part of the code
fragment is the code from the Driver Binding Protocol `Start()` and `Stop()`
functions that create and close the wait event. Typically, a UEFI application
or the UEFI boot manager call `CheckEvent()` or `WaitForEvent()` to see if a
key has been pressed on a input device that supports the Simple Text Input Ex
Protocol. This call to `CheckEvent()` or `WaitForEvent()` causes the
notification function of the wait event in the Simple Text Input Ex Protocol to
be executed. The notification function checks to see if a key has been pressed
on the input device. If the key has been pressed, the wait event is signaled
with a call to `SignalEvent()`. If the wait event is signaled, the UEFI application or UEFI
boot manager then receives an `EFI_SUCCESS` return code and the UEFI
application or UEFI boot manager calls the `ReadKeyStroke()` service of the
`EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL` to read the key that was pressed.

###### Example 47-Create and close a wait event

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Protocol/SimpleTextInEx.h>

EFI_STATUS
KeyboardCheckForKey (
   VOID
  )
{
  //
  // Perform hardware specific action to detect if a key on a
  // keyboard has been pressed.
  //
  return EFI_SUCCESS;
}

VOID
EFIAPI
NotifyKeyboardCheckForKey (
  IN EFI_EVENT                                         Event,
  IN VOID                                              *Context
  )
{
  EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL                    *SimpleInputEx;
  SimpleInputEx = (EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL   *)Context;
  if (!EFI_ERROR (KeyboardCheckForKey ())) {
    gBS->SignalEvent (SimpleInputEx->WaitForKeyEx);
  }
}

EFI_STATUS Status;
EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL                      *SimpleInputEx;

//
// Create a wait event for a Simple Input Protocol
//
Status = gBS->CreateEvent (
                EVT_NOTIFY_WAIT,                       // Type
                TPL_NOTIFY,                            // NotifyTpl
                NotifyKeyboardCheckForKey,             // NotifyFunction
                SimpleInputEx,                         // NotifyContext
                &(SimpleInputEx->WaitForKeyEx)         // Event
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Close the wait event
//
Status = gBS->CloseEvent (SimpleInputEx->WaitForKeyEx);
if (EFI_ERROR (Status)) {
  return Status;
}
```

The code fragment in the following example shows how an Exit Boot Services
event is created using `CreateEvent()` and closed using `CloseEvent()`. In this
example, the `EFI_EVENT` is a global variable. This is the typical
implementation for a UEFI Driver because events of this type are usually
created in the Driver Binding Protocol `Start()` function and closed in the
Driver Binding Protocol `Stop()` function, and the global variable provides an
easy method to close the event in the Driver Binding Protocol `Stop()` function.

This example also contains the function `NotifyExitBootService()`, a template
for the event notification function. It should contain the set of UEFI Driver
specific actions that must be performed when the OS Load or OS Kernel calls
`ExitBootServices()`. This notification function is registered in the call to
`CreateEvent()`. The execution priority level is `TPL_NOTIFY` and the
`NotifyContext` is `NULL` in this example.

**********
**Caution:** The notification function for `ExitBootServices()` is not
allowed to use any of the UEFI Memory Services, either directly or indirectly,
because using those services may modify the UEFI Memory Map and force an error
to be returned from ExitBootServices(). An OS loader or OS Kernel that calls
ExitBootServices() needs to know the state of the memory map at the time
ExitBootServices() was called. The OS loader retrieves the current state of the
memory map by calling `GetMemoryMap()`. If events registered on
ExitBootServices() perform memory allocation or free calls, the memory map may
be modified, and may cause incorrect memory map information to be used by the
OS. The UEFI memory manager detects when the memory map is modified, so the OS
loader always knows that the memory map was not modified `if ExitBootServices()`
returns EFISUCCESS. If the memory map was modified, the OS loader must call `GetMemoryMap()` again to get the current memory map state, and then retry a
call to ExitBootServices(). The modified state is cleared during the call to GetMemoryMap().

###### Example 48-Create and Close an Exit Boot Services Event

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

//
// Global variable for Exit Boot Services event
//
EFI_EVENT mExitBootServicesEvent = NULL;

VOID
EFIAPI
NotifyExitBootServices (
  IN EFI_EVENT                                     Event,
  IN VOID                                          *Context
  )
{
  //
  // Put driver-specific actions here to place controllers into
  // an idle state. No UEFI Memory Service may be used directly
  // or indirectly.
  // 
}

EFI_STATUS                                         Status;

//
// Create an Exit Boot Services event.
//
Status = gBS->CreateEvent (
                  EVT_SIGNAL_EXIT_BOOT_SERVICES,   // Type
                  TPL_NOTIFY,                      // NotifyTpl
                  NotifyExitBootServices,          // NotifyFunction
                  NULL,                            // NotifyContext
                  &mExitBootServicesEvent          // Event
                  );
if (EFI_ERROR (Status)) {
  return Status;
}
  
//
// Close the Exit Boot Services event
//
Status = gBS->CloseEvent (mExitBootServicesEvent);
if (EFI_ERROR (Status)) {
  return Status;
}
```

**The following code fragment has the same functionality as Example 48, above,
but uses CreateEventEx() instead of CreateEvent() to create an event that is
signaled when ExitBootServices() is called. CreateEventEx() supports event
groups that are named by GUID. The Event, Timer, and Task Priority Services
section of the UEFI Specification defines a set of event group GUIDs that are
defined in the EDK II in the MdePkg include file `<Guid/EventGuid.h>`.**

**********
**Caution:** _CreateEventEx() allows creation of more than one timer event
associated with the same event group GUID. Because there is no mechanism for determining which of the timer events associated with the same event group GUID was signaled, it is
recommended that timer events be created with `CreateEvent()` or with `CreateEventEx()` using a `NULL` EventGroup._

###### Example 49-Create and Close an Exit Boot Services Event Group

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Guid/EventGroup.h>

//
// Global variable for Exit Boot Services event
//
EFI_EVENT mExitBootServicesEvent = NULL;

VOID
EFIAPI
NotifyExitBootServices (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  //
  // Put driver-specific actions here to place controllers into
  // an idle state. No UEFI Memory Service may be used directly
  // or indirectly.
  // 
}

EFI_STATUS Status;
  
//
// Create an Exit Boot Services event using event group GUID.
//
Status = gBS->CreateEventEx (
                  EVT_NOTIFY_SIGNAL,              // Type
                  TPL_NOTIFY,                     // NotifyTpl
                  NotifyExitBootServices,         // NotifyFunction
                  NULL,                           // NotifyContext
                  &gEfiEventExitBootServicesGuid, // EventGroup
                  &mExitBootServicesEvent         // Event
                  );
				  
//
// Close the Exit Boot Services event
//
Status = gBS->CloseEvent (mExitBootServicesEvent);
if (EFI_ERROR (Status)) {
  return Status;
}
```

_Example 49_, above, shows how the `CreateEventEx()` function is used to create
an event that is notified when an event group named by GUID is signaled. In
this case, notification functions are called when the OS Loader or OS Kernel
calls `ExitBootServices()`. `CreateEventEx()` also supports creating an event for an
event group named by GUID that causes all the event notification functions
associated with that same event group to be executed when the event is signaled
with `SignalEvent()`.

The _example below_ shows the simplest method of creating, signaling, and
closing an event group named by `gEfiExampleEventGroupGuid`. Notice that Type
is 0 and no notification function, TPL, or context is specified. Since use of
this mechanism is usually in cases where one UEFI image needs to signal events
in other UEFI images, this specific usage of `CreateEventEx()` is rarely used
by UEFI Drivers.

###### Example 50-Create and Signal an Event Group

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Guid/ExampleEventGroup.h>

EFI_STATUS                                   Status;
EFI_EVENT                                    Event;

//
// Create event that is used to signal an event group
//
Status = gBS->CreateEventEx (
                0,                           // Type
                0,                           // NotifyTpl
                NULL,                        // NotifyFunction
                NULL,                        // NotifyContext
                &gEfiExampleEventGroupGuid,  // EventGroup
                &Event                       // Event
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Signal the event causing all notification functions for this
// event group to be executed
//
Status = gBS->SignalEvent (Event);
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Close the event
//
Status = gBS->CloseEvent (Event);
if (EFI_ERROR (Status)) {
  return Status;
}
```

#### 5.1.5.2 SignalEvent()

This service places an event in the signaled state. Use `SignalEvent()`in
implementations of protocols containing an `EFI_EVENT` field informing a
consumer of the protocol when input is ready. The protocols from the _UEFI
Specification_ containing this use case include the Simple Text Input
Protocols, the Pointer Protocols, and the Simple Network Protocol. The complete
list follows:

* `EFI_ABSOLUTE_POINTER_PROTOCOL`

* `EFI_SIMPLE_NETWORK_PROTOCOL`

* `EFI_SIMPLE_POINTER_PROTOCOL`

* `EFI_SIMPLE_TEXT_INPUT_PROTOCOL`

* `EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL`

The example below shows the Simple Text Input Ex Protocol that signals the
`EFI_EVENT` in that protocol when a key press has been detected. The function
`KeyboardCheckForKey()` is a hardware specific function that returns
`EFI_SUCCESS` if a key has been pressed. It returns an error code if a key has
not been pressed. The check is performed at `TPL_NOTIFY` to guarantee that
hardware action checking for a key press is atomic.

###### Example 51-Signal a key press event

```c
#include <Uefi.h>
#include <Library/UefiRuntimeServicesTableLib.h>
#include <Protocol/SimpleTextInEx.h>

EFI_STATUS
EFIAPI
KeyboardCheckForKey (
   VOID
  )
{
  //
  // Perform hardware specific action to detect if a key on a
  // keyboard has been pressed.
  //
  return EFI_SUCCESS;
}

EFI_STATUS                                 Status;
EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL          *SimpleInputEx;
EFI_TPL                                    OldTpl;

//
// Enter critical section
//
OldTpl = gBS->RaiseTPL (TPL_NOTIFY);

//
// Call an internal function to see if a key has been pressed
//
if (!EFI_ERROR (KeyboardCheckForKey ())) {
  //
  // If a key has been pressed, then signal the wait event
  //
  Status = gBS->SignalEvent (SimpleInputEx->WaitForKeyEx);
}

//
// Leave critical section
//
gBS->RestoreTPL (OldTpl);
```

`SignalEvent()` is also used by UEFI Drivers required to signal an event
associated with the completion of a non-blocking I/O operation. The protocols
in the _UEFI Specification_ containing this use-case include the Network
Protocols, SCSI Protocols, ATA Protocols, and the Block I/O 2 Protocol. The
complete list follows:

* `EFI_ARP_PROTOCOL`

* `EFI_IPSEC_PROTOCOL`

* `EFI_IPSEC2_PROTOCOL`

* `EFI_IPSEC_CONFIG_PROTOCOL`

* `EFI_MANAGED_NETWORK_PROTOCOL`

* `EFI_ATA_PASS_THRU_PROTOCOL`

* `EFI_BLOCK_IO2_PROTOCOL`

* `EFI_SCSI_IO_PROTOCOL`

* `EFI_EXT_SCSI_PASS_THRU_PROTOCOL`

* `EFI_DHCP4_PROTOCOL`

* `EFI_IP4_PROTOCOL`

* `EFI_IP4_CONFIG_PROTOCOL`

* `EFI_MTFTP4_PROTOCOL`

* `EFI_TCP4_PROTOCOL` - `EFI_UDP4_PROTOCOL`

* `EFI_FTP4_PROTOCOL`

* `EFI_DHCP6_PROTOCOL`

* `EFI_IP6_PROTOCOL`

* `EFI_IP6_CONFIG_PROTOCOL`

* `EFI_MTFTP6_PROTOCOL`

* `EFI_TCP6_PROTOCOL`

* `EFI_UDP6_PROTOCOL`

#### 5.1.5.3 CheckEvent()

This service checks to see if an event is in the waiting state or the signaled
state. EFI Drivers creating events use this service to determine when an event
has been signaled with `SignalEvent()`. Such events include timer events, those
used to determine when input is available, or events associated with
non-blocking I/O operations.

The example below is an example that creates a one-shot timer event signaled 4
seconds in the future. `CheckEvent()` is called in a loop waiting for the timer
event to be signaled.

###### Example 52-Wait for one-shot timer event to be signaled

```c
#include <Uefi.h>
#include <Library/UefiRuntimeServicesTableLib.h>

EFI_STATUS Status;
EFI_EVENT TimerEvent;

Status = gBS->CreateEvent (
                EVT_TIMER | EVT_NOTIFY_WAIT,   // Type
                TPL_NOTIFY,                    // NotifyTpl
                NULL,                          // NotifyFunction
                NULL,                          // NotifyContext
                &TimerEvent                    // Event
                );
if (EFI_ERROR (Status)) {
  return Status;
}
Status = gBS->SetTimer (
                TimerEvent,
                TimerRelative,
                EFI_TIMER_PERIOD_SECONDS (4)
                );
if (EFI_ERROR (Status)) {
  gBS->CloseEvent (TimerEvent);
  return Status;
}

do {
  Status = gBS->CheckEvent (TimerEvent);
} while (EFI_ERROR (Status));
```

