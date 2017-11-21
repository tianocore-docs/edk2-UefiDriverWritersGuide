<!--- @file
  5.1 Services that UEFI drivers commonly use

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

## 5.1 Services that UEFI drivers commonly use

The following table lists UEFI services commonly used by UEFI drivers.
Following that, discussions briefly describe each service, why they are
commonly used, or the particular circumstance in which they are useful. Code
examples show how the services are typically used by UEFI drivers and are
grouped by Service Type.


###### Table 18-UEFI services that are commonly used by UEFI drivers

| **Service**                            | **Type** | **Service Type** | **Description**                                                                                                  |
| -------------------------------------- | ------ | ----------------- | ------------------------------------------------------------------------------------------------------------ |
| AllocatePool()                         | Boot   | Memory Allocation | Allocates a memory buffer of a particular type.                                                              |
| FreePool()                             | Boot   | Memory Allocation | Frees a previously allocated memory buffer.                                                                  |
| AllocatePages()                        | Boot   | Memory Allocation | Allocates one memory buffer of a particular type with a 4KB aligned start address and a 4KB aligned length.  |
| FreePages()                            | Boot   | Memory Allocation | Frees a memory buffer previously allocated with AllocatePages().                                             |
| CopyMem()                              | Boot   | Miscellaneous     | Copies a buffer from one location to another.                                                                |
| SetMem()                               | Boot   | Miscellaneous     | Initializes the contents of a buffer with a specified value.                                                 |
| InstallMultipleProtocolInterfa ces()   | Boot   | Protocol Handler  | Installs one or more protocol interfaces onto a handle. Replaces the InstallProtocolInterface() service.     |
| UninstallMultipleProtocolInter faces() | Boot   | Protocol Handler  | Uninstalls one or more protocol interfaces from a handle. Replaces the UninstallProtocolInterface() service. |
| LocateHandleBuffer()                   | Boot   | Protocol Handler  | Retrieves a list of handles from the handle database meeting the search criteria. The return buffer is automatically allocated. |
| LocateProtocol()                       | Boot   | Protocol Handler  | Finds the first handle in the handle database supporting the requested protocol.                             |
| OpenProtocol()                         | Boot   | Protocol Handler  | Adds elements to the list of agents consuming a protocol interface.                                          |
| OpenProtocolInformation()            | Boot   | Protocol Handler  | Retrieves the list of agents currently consuming a protocol interface.                                       |
| CloseProtocol()                        | Boot   | Protocol Handler  | Removes elements from the list of agents consuming a protocol interface.                                     |
| RaiseTPL()                             | Boot   | Task Priority     | Raises the task priority level.                                                                              |
| RestoreTPL()                           | Boot   | Task Priority     | Restores/lowers the task priority level.                                                                     |
|                                        |        |                   |                                                                                                              |
| CreateEvent()                          | Boot   | Event             | Creates a general-purpose event structure.                                                                   |
| CreateEventEx()                        | Boot   | Event             | Creates an event structure as part of an event group. _This service is new._                                 |
| CloseEvent()                           | Boot   | Event             | Closes and frees an event structure.                                                                         |
| SignalEvent()                          | Boot   | Event             | Signals an event.                                                                                            |
| CheckEvent()                           | Boot   | Event             | Checks whether an event is in the signaled state.                                                            |
| SetTimer()                             | Boot   | Time-related      | Sets an event to be signaled at a particular time.                                                           |
| Stall()                                | Boot   | Time-related      | Waits for a specified number of microseconds. _This is a time-related service with the highest accuracy._    |
