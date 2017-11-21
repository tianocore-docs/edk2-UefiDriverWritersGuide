<!--- @file
  11.3 GetControllerName() Implementations

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

## 11.3 GetControllerName() Implementations

The `GetControllerName()` service retrieves the name of a controller a driver
is managing or a child the driver has produced. The example below shows an
empty implementation of the `GetControllerName()` service for the Component
Name 2 Protocol. The recommended implementation style shown here allows the
same `GetControllerName()` service implementation to be shared between both the
Component Name Protocol and the Component Name 2 Protocol.

###### Example 130-GetControllerName () Service

```c
#include <Uefi.h>
#include <Protocol/ComponentName2.h>

EFI_STATUS
EFIAPI
AbcGetControllerName (
  IN  EFI_COMPONENT_NAME2_PROTOCOL  *This,
  IN  EFI_HANDLE                    ControllerHandle,
  IN  EFI_HANDLE                    ChildHandle,       OPTIONAL
  IN  CHAR8                         *Language,
  OUT CHAR16                        **ControllerName
  )
{
}
```

The Component Name Protocols are available only for devices currently under a
driver's management. Because UEFI supports connecting the minimum number of
drivers and devices required to establish console and gain access to the boot
device, there may be many unconnected devices for which a name may not be
retrieved.
