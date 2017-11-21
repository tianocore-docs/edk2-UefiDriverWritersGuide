
<!--- @file
  13.2.1 Device Drivers

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

### 13.2.1 Device Drivers

Device drivers that implement `RunDiagnostics()` must verify that _ChildHandle_
is `NULL` and that _ControllerHandle_ represents a device that the device
driver is currently managing. In addition, RunDiagnostics() must verify that
the requested Language is in the set of languages that the UEFI Driver
supports. The following example shows the steps required to check these
parameters. If these checks pass, the diagnostic are executed and results are
returned. In this specific example, the driver opens the PCI I/O Protocol in
its Driver Binding `Start()` function. This is why `gEfiPciIoProtocolGuid` is
used in the call to the EDK II Library `UefiLib` function
`EfiTestManagedDevice()` that checks to see if the UEFI Drivers that is
providing this `RunDiagnostics()` service is currently managing
_ControllerHandle_. If the private context structure is required, typically the
UEFI Boot Service `OpenProtocol()` is used to open one of the UEFI Driver
produced protocols on _ControllerHandle_ and then use a `CR()` based macro to
retrieve a pointer to the private context structure.

###### Example 148-RunDiagnostics() for a Device Driver

```c
#include <Uefi.h>
#include <Protocol/DriverDiagnostics2.h>
#include <Protocol/PciIo.h>
#include <Library/BaseMemoryLib.h>
#include <Library/UefiLib.h>

EFI_STATUS 
EFIAPI
AbcRunDiagnostics (
  IN  EFI_DRIVER_DIAGNOSTICS2_PROTOCOL  *This,
  IN  EFI_HANDLE                        ControllerHandle,
  IN  EFI_HANDLE                        ChildHandle,       OPTIONAL
  IN  EFI_DRIVER_DIAGNOSTIC_TYPE        DiagnosticType,
  IN  CHAR8                             *Language,
  OUT EFI_GUID                          **ErrorType,
  OUT UINTN                             *BufferSize,
  OUT CHAR16                            **Buffer
  )
{
  EFI_STATUS  Status;
  CHAR8       *SupportedLanguages;
  BOOLEAN     Rfc4646Language;
  BOOLEAN     Found;
  UINTN       Index;
  
  //
  // ChildHandle must be NULL for a Device Driver
  //
  if (ChildHandle != NULL) {
    return EFI_UNSUPPORTED;
  }
  
  //
  // Make sure this driver is currently managing ControllerHandle
  //
  Status = EfiTestManagedDevice (
             ControllerHandle,
             gAbcDriverBinding.DriverBindingHandle,
             &gEfiPciIoProtocolGuid
             );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // Check input parameters
  //
  if (Language == NULL || ErrorType == NULL ||
      BufferSize == NULL || Buffer == NULL ) {
    return EFI_INVALID_PARAMETER;
  }
  
  //
  // Make sure Language is in the set of Supported Languages
  //
  SupportedLanguages = This->SupportedLanguages;
  Rfc4646Language = (BOOLEAN)(This == &gAbcDriverDiagnostics2);
  Found = FALSE;
  while (*SupportedLanguages != 0) {
    if (Rfc4646Language) {
      for (Index = 0;
           SupportedLanguages[Index] != 0 && SupportedLanguages[Index] != ';
           ';
           Index++);
      if ((AsciiStrnCmp (SupportedLanguages, Language, Index) == 0) &&
          (Language[Index] == 0)) {
        Found = TRUE;
        break;
      }
      SupportedLanguages += Index;
      for (;
           *SupportedLanguages != 0 && *SupportedLanguages == ';
           ';
           SupportedLanguages++);
    } else {
      if (CompareMem (Language, SupportedLanguages, 3) == 0) {
        Found = TRUE;
        break;
      }
      SupportedLanguages += 3;
    }
  }
  //
  // If Language is not a member of SupportedLanguages, then return EFI_UNSUPPORTED
  //
  if (!Found) {
    return EFI_UNSUPPORTED;
  }
  
  //
  // Perform Diagnostics Algorithm on ControllerHandle for the
  // type of diagnostics requested in DiagnosticsType
  //
  // Return results in ErrorType, Buffer, and BufferSize
  //
  // If Diagnostics Algorithm fails, then return EFI_DEVICE_ERROR
  //
  
  return EFI_SUCCESS;
}
```

To verify the operation of the controller, diagnostic algorithms typically use
the services of the protocols the driver produces and the services of the
protocols the driver consumes. For example, a PCI device driver that consumes
the PCI I/O Protocol and produces the Block I/O Protocol can use the services
of the PCI I/O Protocol to verify the operation of the PCI controller. Use the
Block I/O Services to verify that the entire driver is working as expected.
