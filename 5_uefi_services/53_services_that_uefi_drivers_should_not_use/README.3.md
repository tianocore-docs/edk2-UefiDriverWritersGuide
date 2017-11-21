<!--- @file
  5.3 Services that UEFI drivers should not use

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

## 5.3 Services that UEFI drivers should not use

The following table lists the UEFI services that should _not_ be used by UEFI
drivers. These services may be used by components other than UEFI Drivers, or
these services may have been replaced by newer services and should no longer be
used by UEFI Drivers. The following sections describe why each of these
services should not be used in UEFI drivers and are grouped by Service Type.

###### Table 20-UEFI services that should not be used by UEFI drivers

| **Service**                  | **Type** | **Service Type** | **Notes**                                                                        |
| ---------------------------- | -------- | ---------------- | -------------------------------------------------------------------------------- |
| InstallProtocolInterface()   | Boot    | Protocol Handler | Installs a protocol interface on a device handle. Replaced by      InstallMultipleProtocolInterfaces().                                                                                                           |
| UninstallProtocolInterface() | Boot    | Protocol Handler | Removes a protocol interface from a device handle. Replaced by     UninstallMultipleProtocolInterfaces().                                                                                                         |
| HandleProtocol()             | Boot    | Protocol Handler | Queries a handle to determine if it supports a specified protocol. Replaced by OpenProtocol().                                                                                                                                |
| LocateHandle()               | Boot    | Protocol Handler | Returns an array of handles that support a specified protocol. This service has been replaced by LocateHandleBuffer().                                                                                                                          |
| ProtocolsPerHandle()         | Boot    | Protocol Handler | Retrieves the list of protocols installed on a handle. The return buffer is automatically allocated. This service has been replaced with: The Start function in the UEFI Driver Binding Protocol.                                        |
| RegisterProtocolNotify()     | Boot    | Protocol Handler | Registers an event that is to be signaled whenever an interface is installed for a specified protocol. This service has been replaced with: The Supported() function in the UEFI Driver Binding Protocol.                                   |
| UnloadImage()                | Boot    | Image            | Used to unload a previously loaded UEFI Driver.                                                                                                                                        |
| GetNextVariableName()        | Runtime | Variable         | Used to walk the list of UEFI variables that are maintained through the UEFI variable services. Use of this service is not usually necessary.                                                                                                  |
| SetWatchDogTimer()           | Boot    | Time-related     | Sets the current local time and date information. UEFI drivers should not use this service; UEFI drivers should not modify the system time or the wakeup timer.                                                                                 |
| SetTime()                    | Runtime | Time-related     | Sets the current local time and date information. UEFI drivers should not use this service; UEFI drivers should not modify the system time or the wakeup timer.                                                                                 |
| GetWakeupTime()              | Runtime | Time-related     | Returns the current wakeup alarm clock setting. UEFI drivers should not use this service; the watchdog timer is managed from the UEFI boot manager.                                                                                          |
| SetWakeupTime()              | Runtime | Time-related     | Sets the system wakeup alarm clock time. UEFI drivers should not use this service; the watchdog timer is managed from the UEFI boot manager.                                                                                                   |
| GetMemoryMap()               | Boot    | Memory Allocation | Returns the current boot services memory map and memory map key.                 |
| ExitBootServices()           | Boot    | Special          | This service hands control of the platform from the UEFI conformant firmware to an OS. UEFI drivers must never use this service.                                                                                                           |
| SetVirtualAddressMap()       | Runtime | Special          | This service is used only by UEFI OS loaders or OS kernels for operating systems that wish to call UEFI runtime services using virtual addresses. UEFI drivers must never use this service.                                                  |
| QueryCapsuleCapabilities()   | Runtime | Special          | Test to see if a capsule or capsules can be updated via UpdateCapsule().         |
| UpdateCapsule()              | Runtime | Special          | Allows the operating system to pass information to firmware.                     |
| ResetSystem()                | Runtime | Special          | Resets and sets a watchdog timer used during boot services time. UEFI drivers should not use this service; the watchdog timer is managed from the UEFI boot manager.                                                                             |
| Exit()                       | Boot    | Special          | UEFI drivers should not use this service. This service is typically used by applications.    |
| GetNextHighMonotonicCount    | Runtime | Special          | Provides a 64-bit monotonic counter that is guaranteed to increase.              |


