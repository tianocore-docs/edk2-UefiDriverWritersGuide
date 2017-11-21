<!--- @file
  13.2 RunDiagnostics() Implementations

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

## 13.2 RunDiagnostics() Implementations

The `RunDiagnostics()` service runs diagnostics on the controller a driver is
managing or a child the driver has produced. This service is not allowed to use
any of the consoleI/O-related protocols. Instead, the results of the
diagnostics are returned to the caller in a buffer. The caller may choose to
log the results or display them. The example below shows an empty
implementation of the `RunDiagnostics()` service for the Driver Diagnostics 2
Protocol. The recommended implementation style shown allows the same `RunDiagnostics()` service implementation to be shared between the Driver
Diagnostics Protocol and the Driver Diagnostics 2 Protocol.

###### Example 147-RunDiagnostics() Service

```c
#include <Uefi.h>
#include <Protocol/DriverDiagnostics2.h>

EFI_STATUS
EFIAPI
AbcRunDiagnostics (
  IN  EFI_DRIVER_DIAGNOSTICS2_PROTOCOL  *This,
  IN  EFI_HANDLE                        ControllerHandle,
  IN  EFI_HANDLE                        ChildHandle,  OPTIONAL
  IN  EFI_DRIVER_DIAGNOSTIC_TYPE        DiagnosticType,
  IN  CHAR8                             *Language,
  OUT EFI_GUID                          **ErrorType,
  OUT UINTN                             *BufferSize,
  OUT CHAR16                            **Buffer
  )
{
}
```

The _DiagnosticType_ parameter tells the driver the type of diagnostics to
perform. Standard diagnostics must be implemented and test basic functionality.
They should complete in less than 30 seconds. Extended diagnostics are
recommended and may take more than 30 seconds to execute. Manufacturing
diagnostics are intended to be used in manufacturing and test environments.

_ErrorType_, _BufferSize_, and _Buffer_ are the return parameters that report
the results of the diagnostic. _Buffer_ begins with a `NULL`-terminated Unicode
string so the caller of the `RunDiagnostics()` service can display a
human-readable diagnostic result. _ErrorType_ is a GUID that defines the format
of the data buffer following the `NULL`-terminated Unicode string. _BufferSize_
is the size of _Buffer_ that includes the `NULL`-terminated Unicode string and
the GUID-specific data buffer. The implementation of `RunDiagnostics()` must
allocate _Buffer_ using the service `AllocatePool()`, and it is the caller's
responsibility to free this buffer with `FreePool()`.

The Driver Diagnostics Protocols are available only for devices a driver is
currently managing. Because UEFI supports connecting the minimum number of
drivers and devices that are required to establish console and gain access to
the boot device, there may be many unconnected devices that support
diagnostics. As a result, when the user wishes to enter a platform
configuration mode, the UEFI boot manager must connect all drivers to all
devices, so that the user can be shown all devices supporting diagnostics.
