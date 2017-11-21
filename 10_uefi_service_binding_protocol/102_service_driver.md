<!--- @file
  10.2 Service Driver

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

## 10.2 Service Driver

If the UEFI Driver is a Service Driver, the Service Binding Protocol is
installed in the driver entry point. The following example shows an
implementation of a Service Binding Protocol that is installed into the Handle
Database in the driver entry point. A Service Binding Protocol is always paired with another protocol so, for this example, the paired protocol is the `ABC_PROTOCOL`.

Global variables are declared for the handle on which the Service Binding
Protocol is installed, the instance of the Service Binding Protocol, and an
instance of the `ABC_PROTOCOL`. The `ABC_PROTOCOL` instance is installed onto a
new handle every time the Service Binding Protocol service `CreateChild()` is
called. The `ABC_PROTOCOL` is uninstalled from a child handle every time the
Service Binding Protocol service `DestroyChild()` is called.

###### Example 125-Service Binding Protocol for Service Driver

```c
#include <Uefi.h>
#include <Protocol/ServiceBinding.h>
#include <Library/UefiBootServicesTableLib.h>

typedef struct {
  UINT32 AbcField;
} ABC_PROTOCOL;

EFI_HANDLE gAbcServiceBindingHandle = NULL;

EFI_SERVICE_BINDING_PROTOCOL gAbcServiceBinding = {
  AbcCreateChild,
  AbcDestroyChild
};

ABC_PROTOCOL gAbc = {
  0
};

EFI_STATUS
EFIAPI
AbcCreateChild (
  IN     EFI_SERVICE_BINDING_PROTOCOL  *This,
  IN OUT EFI_HANDLE                    *ChildHandle
  )
{
  EFI_HANDLE  NewHandle;
  NewHandle = NULL;
  return gBS->InstallMultipleProtocolInterfaces (
                &NewHandle,
                &gAbcProtocolGuid,
                &gAbc,
                NULL
                );
}

EFI_STATUS
EFIAPI
AbcDestroyChild (
  IN EFI_SERVICE_BINDING_PROTOCOL  *This,
  IN EFI_HANDLE                    ChildHandle
  )
{
  return gBS->UninstallMultipleProtocolInterfaces (
                ChildHandle,
                &gAbcProtocolGuid,
                &gAbc,
                NULL
                );
}

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  //
  // Install Service Binding Protocol for ABC onto a new handle
  //
  return gBS->InstallMultipleProtocolInterfaces (
                &gAbcServiceBindingHandle,
                &gAbcServiceBindingProtocolGuid,
                &gAbcServiceBinding,
                NULL
                );
}
```
