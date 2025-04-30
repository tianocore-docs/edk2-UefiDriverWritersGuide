<!--- @file
  5.2.10 InstallConfigurationTable()

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

### 5.2.10 InstallConfigurationTable()

This service is used to add, update, or remove an entry in the list of
configuration table entries maintained in the UEFI System Table. These entries
are typically used to pass information from the UEFI pre-boot environment to
the operating system environment.

The configuration table entries are composed of a GUID and a pointer to a
buffer. The GUID defines the type of memory that the buffer must use. If an
operating system requires a configuration table entry that is allocated from a
memory type that is not preserved after `ExitBootServices()`, then the OS
Loader or OS Kernel must make a copy of the data structure prior calling
`ExitBootServices()`.

A UEFI Driver has a limited set of options to pass information into the
operating system environment. These include:

* Protocols

* UEFI Variables

* Configuration Table Entries

The services required to locate protocols in the Handle Database are not
available after `ExitBootServices()`, so information passed up through
protocols must be located by the OS Loader or OS Kernel prior to calling
`ExitBootServices()`. UEFI Variables are good for small amounts of data, but
may consume the scarce variable resources and access to variable storage may be
slower than system memory. A configuration table entry is good for larger
amounts of data generated each boot and it is stored in system memory. The
_UEFI Specification_ defines a set of GUIDs for standard configuration table
entries that includes:

* ACPI Tables

* SMBIOS Tables

* SAL System Table (IPF only)

* MPS Tables

* Debug Image Info Tables

* Image Execution Information Table

* Exported HII Database

* User Information Table

* Capsules

* UNDI Configuration Table

Most of these usages are handled by the UEFI system firmware. The one usage
impacting UEFI Drivers is the UNDI Configuration Table that is produced by a
UEFI UNDI Driver for a Network Interface Controller (NIC). UEFI Drivers are
allowed to define new GUIDs for new configuration table entries to pass
information from the UEFI pre-boot environment to the OS environment.

The following code fragment shows how an UNDI driver can add or update an UNDI
Configuration Table entry to the list of configuration table entries maintained
in the UEFI System Table.

###### Example 79-Add or update a configuration table entry

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/MemoryAllocationLib.h>

EFI_STATUS Status;
UNDI_CONFIG_TABLE *UndiConfigTable;

//
// Allocate and zero UNDI_CONFIG_TABLE from EfiRuntimeServicesData
//
UndiConfigTable = (UNDI_CONFIG_TABLE *)AllocateRuntimeZeroPool (
                sizeof (UNDI_CONFIG_TABLE)
                );

//
// Initialize UNDI_CONFIG_TABLE
//

//
// Add or update a configuration table
//
Status = gBS->InstallConfigurationTable (
                &gEfiNetworkInterfaceIdentifierProtocolGuid_31,
                UndiConfigTable
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

The code fragment below shows how an UNDI driver can remove an UNDI

Configuration Table entry from the list of configuration table entries
maintained in the UEFI System Table.

###### Example 80-Add or update a configuration table entry

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS Status;

//
// Remove a configuration table
//
Status = gBS->InstallConfigurationTable (
                &gEfiNetworkInterfaceIdentifierProtocolGuid_31,
                NULL
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

#### 5.2.10.1 WaitForEvent()

This service stops execution until an event is signaled and is only allowed to
be called at a priority level of `TPL_APPLICATION`. This means that
`WaitForEvent()` may not be used from an event notification function because
event notification functions always execute at priority levels above
`TPL_APPLICATION`. If a UEFI Driver needs to know the current state of an
event, the `CheckEvent()` service should be used instead of `WaitForEvent()`.
`WaitForEvent()` may be used by UEFI Applications. The typical use case is to
wait for input from a device such as a keyboard or mouse as part of a user
interface. There are a few older protocols that UEFI Drivers may produce that
interact with the user and the implementation of these protocols could use
`WaitForEvent()`. For example, the `SetOptions()` function in the Driver
Configuration Protocol.

The following code fragment shows how `WaitForEvent()` is used to wait for one
of two events to be signaled. One event is signaled if a key is pressed on the
console input device from the UEFI System Table. The other event is a one-shot
timer that is signaled after waiting for 1 second. `WaitForEvent()` does not
return until either a key is pressed or 1 second has passed. This can be used
to wait for a key and also update the console with status information once a
second. Status is set to `EFI_SUCCESS` is a key is pressed and `EFI_TIMEOUT` if
no key is pressed.

###### Example 81-Wait for key press or timer event

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS Status;
EFI_EVENT WaitList[2];
UINTN Index;

//
// Add ConIn event from the UEFI System Table to the array of events
//
WaitList[0] = &gST->ConIn->WaitForKey;

//
// Add timer event that fires in 1 second to the array of events
//
Status = gBS->CreateEvent (
                EVT_TIMER | EVT_NOTIFY_WAIT,  // Type
                TPL_NOTIFY,                   // NotifyTpl
                NULL,                         // NotifyFunction
                NULL,                         // NotifyContext
                &WaitList[1]                  // Event
                );
if (EFI_ERROR (Status)) {
  return Status;
}

Status = gBS->SetTimer (
                WaitList[1],
                TimerRelative,
                EFI_TIMER_PERIOD_SECONDS (1)
                );
if (EFI_ERROR (Status)) {
  gBS->CloseEvent (WaitList[1]);
  return Status;
}

//
// Wait for the console input or the timer to be signaled
//
Status = gBS->WaitForEvent (2, WaitList, &Index);

//
// Close the timer event
//
gBS->CloseEvent (WaitList[1]);

//
// If the timer event expired return EFI_TIMEOUT
//
if (!EFI_ERROR (Status) && Index == 1) {
  Status = EFI_TIMEOUT;
}
```
