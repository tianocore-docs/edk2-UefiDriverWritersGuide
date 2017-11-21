<!--- @file
  5.2 Services that UEFI drivers rarely use

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

## 5.2 Services that UEFI drivers rarely use

_Table 19_ lists UEFI services rarely used by UEFI drivers. The following
sub-topics briefly describe each service, why they are rarely used, or the
particular circumstance in which they are useful. The code examples show how
the services are typically used by UEFI drivers and are grouped by Service Type.

###### Table 19-UEFI services that are rarely used by UEFI drivers

| **Service**                   | **Type** | **Service Type** | **Notes**       |
| ----------------------------- | ------- | ----------------- | ------------------------------------------------------------------------------------------------------------ |
| ConnectController()           | Boot    | Protocol Handler | Uses a set of precedence rules to find the best set of drivers to manage a controller.                       |
| DisconnectController()        | Boot    | Protocol Handler | Informs a set of drivers to stop managing a controller.                                                      |
| ReinstallProtocolInterface()  | Boot    | Protocol Handler | Reinstalls a protocol interface on a device handle.                                                          |
| LocateDevicePath()            | Boot    | Protocol Handler | Locates a device handle supporting a specific protocol and having the closest matching device path. UEFI drivers should use the services on the `ControllerHandle` passed into the `Supported()` and `Start()` functions of the driver's `EFI_DRIVER_BINDING_PROTOCOL`.                      |
| LoadImage()                   | Boot    | Image            | Used only by bus drivers that can load, start, and potentially unload UEFI drivers stored in other images in some other location on the child devices of the bus.  |
| StartImage()                  | Boot    | Image            | Used only by bus drivers that can load, start, and potentially unload UEFI drivers stored in other images in some other location on the child devices of the bus.  |
| GetVariable()                 | Runtime | Variable         | Returns the value of a variable.                                                                             |
| SetVariable()                 | Runtime | Variable         | Sets the value of a variable.                                                                                |
| QueryVariableInfo()           | Runtime | Variable         | Returns information about the EFI variables.                                                                 |
| GetTime()                     | Runtime | Time-related     | Returns the current time and date, and the time-keeping capabilities of the platform.                        |
| CalculateCrc32()              | Boot    | Miscellaneous    | Maintains the checksum of the UEFI System Table, UEFI boot services table, and UEFI runtime services table.  |
| ConvertPointer()              | Runtime | Miscellaneous    | Sometimes used by UEFI runtime drivers. This service should never be used by UEFI boot service drivers.      |
| InstallConfigurationTable()   | Boot    | Miscellaneous    | Adds, updates, or removes a configuration table from the UEFI system table.                                  |
| WaitForEvent()                | Boot    | Event            | Stops execution until an event is signaled.                                                                  |
| GetNextMonotonicCount()       | Boot    | Special          | Provides a 64-bit monotonic counter that is guaranteed to increase.                                          |
