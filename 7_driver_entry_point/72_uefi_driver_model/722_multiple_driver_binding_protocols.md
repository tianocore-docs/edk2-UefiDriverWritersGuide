<!--- @file
  7.2.2 Multiple Driver Binding Protocols

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

### 7.2.2 Multiple Driver Binding Protocols

If a UEFI driver supports more than one parent I/O abstraction, the driver
should produce a Driver Binding Protocol for each of the parent I/O
abstractions. For example, a UEFI driver could be written to support more than
one type of hardware device (for example, USB and PCI). If code can be shared
for the common features of a hardware device, then such a driver might save
space and reduce maintenance. Example drivers in the EDK II that produce more
than one Driver Binding Protocol are the console platform driver and the
console splitter driver. These drivers contain multiple Driver Binding
Protocols because they depend on multiple console-related parent I/O
abstractions.

The first Driver Binding Protocol is typically installed onto the _ImageHandle_
of the UEFI driver and additional Driver Binding Protocols are installed onto
new handles. The UEFI driver library functions used in the previous two
examples support the creation of new handles by passing in a `NULL` for the
fourth argument. The example below shows the driver entry point for a driver
that produces two instances of the Driver Binding Protocol with no optional
driver-related protocols. When multiple Driver Binding Protocols are produced
by a single driver, the optional driver-related protocols are installed onto
the same handles as those of the Driver Binding Protocols.

###### Example 91-Multiple Driver Binding Protocols

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/UefiLib.h>
#include <Library/DebugLib.h>

#define ABC_VERSION 0x10

EFI_DRIVER_BINDING_PROTOCOL gAbcFooDriverBinding = {
  AbcFooSupported,
  AbcFooStart,
  AbcFooStop,
  ABC_VERSION,
  NULL,
  NULL
};

EFI_DRIVER_BINDING_PROTOCOL gAbcBarDriverBinding = {
  AbcBarSupported,
  AbcBarStart,
  AbcBarStop,
  ABC_VERSION,
  NULL,
  NULL
};

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  EFI_STATUS  Status;
  
  //
  // Install first Driver Binding Protocol onto ImageHandle
  //
  Status = EfiLibInstallDriverBinding (
             ImageHandle,                   // ImageHandle
             SystemTable,                   // SystemTable
             &gAbcFooDriverBinding,         // DriverBinding
             ImageHandle                    // DriverBindingHandle
             );
  ASSERT_EFI_ERROR (Status);
  
  //
  // Install second Driver Binding Protocol onto a new handle
  //
  Status = EfiLibInstallDriverBinding (
             ImageHandle,                  // ImageHandle
             SystemTable,                  // SystemTable
             &gAbcBarDriverBinding,        // DriverBinding
             NULL                          // DriverBindingHandle
             );
  ASSERT_EFI_ERROR (Status);
  
  return EFI_SUCCESS;
}
```
