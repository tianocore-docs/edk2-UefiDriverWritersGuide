<!--- @file
  5.2.1 ConnectController() and DisconnectController()

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

### 5.2.1 ConnectController() and DisconnectController()

These services request UEFI Drivers to start or stop managing controllers in a
platform. They are typically used by the UEFI Boot Manager to connect the
devices required to boot an operating system. These services may also be used
by a UEFI Boot Manager to connect all devices in the platform if the user
chooses to enter platform setup. OS Loaders and OS Installers may also use
these services to connect additional devices required to complete an OS boot or
OS installation operation.

Additionally, UEFI applications, such as the UEFI Shell, may use these services
to test the functionality of a UEFI Driver under test. The UEFI Shell commands
using these services are `connect`, `disconnect`, and `reconnect`. A common
test sequence a UEFI Driver developer may use to test the functionality of a
new UEFI Driver is:

* Load the UEFI Driver.

* Connect the UEFI Driver.

* Test functionality of protocols produced by the UEFI Driver.

* Disconnect the UEFI Driver.

* Unload the UEFI Driver.

* Fix known issues with the UEFI Driver and repeat.

The use of `ConnectController()` and `DisconnectController()` in UEFI Driver
implementations is less common and is usually restricted to UEFI Drivers
managing hot-plug capable busses and unloadable UEFI Drivers.

#### 5.2.1.1 Hot Plug Operations

To facilitate a hot-add operation on a hot-plug capable bus, use
`ConnectController()`to connect UEFI Drivers to the hot-added device. Likewise,
to facilitate a hot-remove operation on a hot-plug capable bus, use
`DisconnectController()`to request that UEFI Drivers stop managing the removed
device. Just because a bus is capable of supporting hot-plug events does not
necessarily mean that the UEFI driver for that bus type must support those
hot-plug events. Support for hot-plug events in the pre-boot environment is
dependent on the platform requirements for each bus type.

The best example of the hot-plug this use case in the EDK II is the USB Bus
Driver in `MdeModulePkg/Bus/Usb/UsbBusDxe`. The USB bus driver in the EDK II does not
create any child handles in its Driver Binding Protocol `Start()` function. Instead, it
registers a periodic timer event.

When the timer period expires, the timer event's notification function is
called and that notification function examines all USB root ports and USB hubs
to see if any USB devices have been added or removed. If a USB device is added,
a child handle is created with a Device Path Protocol and a USB I/O Protocol.
`ConnectController()` is then called to allow USB device drivers to connect to
the newly added USB device. If a USB device has been removed,
`DisconnectController()` is called to stop the USB device drivers from managing
the removed USB device.

The following code fragment shows how `ConnectController()` is used to perform
a recursive connect operation in response to a hot-add operation.

<div style="page-break-after: always;"></div>

###### Example 58-Recursive connect in response to a hot-add operation

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS Status;
EFI_HANDLE ChildHandle;

//
// Recursively connect all drivers to the hot-added device
//
Status = gBS->ConnectController (ChildHandle, NULL, NULL, TRUE);
if (EFI_ERROR (Status)) {
  return Status;
}
```

The code fragment below shows how `DisconnectController()` is used to perform a
recursive disconnect operation in response to a hot-remove operation.

###### Example 59-Recursive disconnect in response to a hot-remove operation

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS Status;
EFI_HANDLE ChildHandle;

//
// Recursively disconnect all drivers from the hot-removed device
//
Status = gBS->DisconnectController (
                ChildHandle,
                NULL,
                NULL
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

#### 5.2.1.2 Driver Unload Operations

Use the `DisconnectController()` service, from unloadable UEFI drivers, to
disconnect the UEFI driver from the device(s) it is managing. The
`DisconnectController()` service is called from the Unload() function that is
registered in the Loaded Image Protocol for the UEFI Driver

The following code fragment shows a simple algorithm that a UEFI Driver can use
to disconnect the UEFI Driver from all the devices in the system that it is
currently managing.

It first retrieves the list of all the handles in the handle database, then
disconnects the UEFI driver from each of those handles.

A UEFI Driver could implement a more efficient algorithm if the UEFI Driver
kept a list of the controller handles it manages. It could then call
`DisconnectController()` for each of the controller handles in that list.

###### Example 60-Disconnect a UEFI Driver from all handles

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/MemoryAllocationLib.h>

EFI_STATUS Status;
EFI_HANDLE *HandleBuffer;
UINTN HandleCount;
UINTN Index;

//
// Retrieve array of all handles in the handle database
//
Status = gBS->LocateHandleBuffer (
                AllHandles,
                NULL,
                NULL,
                &HandleCount,
                &HandleBuffer
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Disconnect the current driver from all handles in the handle database
//
for (Index = 0; Index < HandleCount; Index++) {
  Status = gBS->DisconnectController (
                  HandleBuffer[Index],
                  gImageHandle,
                  NULL
                  );
}

//
// Free the array of handles
//
FreePool (HandleBuffer);
```
