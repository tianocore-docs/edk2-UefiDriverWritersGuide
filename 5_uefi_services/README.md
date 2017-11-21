<!--- @file
  5 UEFI Services

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

# 5 UEFI Services

This chapter focuses on the UEFI services that apply to the implementation of
UEFI drivers. This includes descriptions of those services, along with code
examples, that demonstrate how a UEFI driver typically uses those services. The
EDK II provides a number of library functions that simplify the use of UEFI
services as well as UEFI driver improvements in maintainability, portability,
readability, robustness, and size. Additional descriptions and code examples
using EDK II library functions also appear where applicable.

The UEFI Boot Services and UEFI Runtime Services available to UEFI Drivers fall
into three general areas:

* Commonly used services

* Rarely used services

* Services that _should not_ be used from a UEFI driver

The full function prototypes and descriptions for each service, and their
arguments, are available in the Boot Services and Runtime Services chapters of
the _UEFI Specification_. The full function prototypes and descriptions of the
EDK II library functions, and their arguments, are available in the _EDK II
MdePkg Package Document_ and the _EDK II MdeModulePkg Package Document_.

The following table lists alphabetically all UEFI Boot and Runtime Services.

<div style="page-break-after: always;"></div>

###### Table 17-Alphabetical listing of UEFI services

|**Service**                            | **Type** | **Service Type**  |
| ------------------------------------- | -------- | ----------------- |
| AllocatePool()                        | Boot    | Memory Allocation |
| AllocatePages()                       | Boot    | Memory Allocation |
| CalculateCrc32()                      | Boot    | Miscellaneous     |
| CheckEvent()                          | Boot    | Event             |
| CloseEvent()                          | Boot    | Event             |
| CloseProtocol()                       | Boot    | Protocol Handler  |
| ConnectController()                   | Boot    | Protocol Handler  |
| ConvertPointer()                      | Runtime | Miscellaneous     |
| CopyMem()                             | Boot    | Miscellaneous     |
| CreateEvent()                         | Boot    | Event             |
| CreateEventEx()                       | Boot    | Event             |
| DisconnectController()                | Boot    | Protocol Handler  |
| Exit()                                | Boot    | Special           |
| ExitBootServices()                    | Boot    | Special           |
| FreePages()                           | Boot    | Memory Allocation |
| FreePool()                            | Boot    | Memory Allocation |
| GetMemoryMap()                        | Boot    | Memory Allocation |
| GetNextMonotonicCount()               | Boot    | Special           |
| GetNextHighMonotonicCount()           | Runtime | Special           |
| GetNextVariableName()                 | Runtime | Variable          |
| GetTime()                             | Runtime | Time-related      |
| GetVariable()                         | Runtime | Variable          |
| GetWakeupTime()                       | Runtime | Time-related      |
| HandleProtocol()                      | Boot    | Protocol Handler  |
| InstallConfigurationTable()           | Boot    | Miscellaneous     |
| InstallMultipleProtocolInterfaces()   | Boot    | Protocol Handler  |
| InstallProtocolInterface()            | Boot    | Protocol Handler  |
| LoadImage()                           | Boot    | Image             |
| LocateDevicePath()                    | Boot    | Protocol Handler  |
| LocateHandle()                        | Boot    | Protocol Handler  |
| LocateHandleBuffer()                  | Boot    | Protocol Handler  |
| LocateProtocol()                      | Boot    | Protocol Handler  |
| OpenProtocol()                        | Boot    | Protocol Handler  |
| OpenProtocolInformation()             | Boot    | Protocol Handler  |
| ProtocolsPerHandle()                  | Boot    | Protocol Handler  |
| QueryCapsuleCapabilities()            | Runtime | Special           |
| QueryVariableInfo()                   | Runtime | Variable          |
| RaiseTPL()                            | Boot    | Task Priority     |
| RegisterProtocolNotify()              | Boot    | Protocol Handler  |
| ReinstallProtocolInterface()          | Boot    | Protocol Handler  |
| ResetSystem()                         | Runtime | Special           |
| RestoreTPL()                          | Boot    | Task Priority     |
| SetMem()                              | Boot    | Miscellaneous     |
| SetTime()                             | Runtime | Time-related      |
| SetTimer()                            | Boot    | Time-related      |
| SetVariable()                         | Runtime | Variable          |
| SetVirtualAddressMap()                | Runtime | Special           |
| SetWakeupTime()                       | Runtime | Time-related      |
| SetWatchDogTimer()                    | Boot    | Time-related      |
| StartImage()                          | Boot    | Image             |
| SignalEvent()                         | Boot    | Event             |
| Stall()                               | Boot    | Time-related      |
| UninstallMultipleProtocolInterfaces() | Boot    | Protocol Handler  |
| UninstallProtocolInterface()          | Boot    | Protocol Handler  |
| UnloadImage()                         | Boot    | Image             |
| UpdateCapsule()                       | Runtime | Special           |
| WaitForEvent()                        | Boot    | Event             |
