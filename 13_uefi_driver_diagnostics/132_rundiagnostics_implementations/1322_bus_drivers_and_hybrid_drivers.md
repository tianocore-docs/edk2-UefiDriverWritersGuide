<!--- @file
  13.2.2 Bus Drivers and Hybrid Drivers

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

### 13.2.2 Bus Drivers and Hybrid Drivers

Bus drivers and hybrid drivers implementing the Driver Diagnostics Protocols
must verify that _ControllerHandle_ and _ChildHandle_ represent a device
currently managed by the driver. In addition, `RunDiagnostics()` must verify
that the requested _Language_ is in the set of languages supported by the UEFI
Driver. The following example shows the steps required to check these
parameters and also retrieve the private context data structure. If the checks
pass, the diagnostics are executed and results returned.

In this specific example, the driver opens the PCI I/O Protocol in its Driver
Binding Start() function. This is why `gEfiPciIoProtocolGuid` is used in the call to the EDK II Library `UefiLib` function `EfiTestManagedDevice()`. It checks to see if the UEFI Drivers providing the `RunDiagnostics()` service is currently managing _ControllerHandle_. If the private context structure is required, then, typically, the UEFI Boot Service

`OpenProtocol()` is used to open one of the protocols on _ControllerHandle_
that the UEFI Driver produced and then uses a `CR()` based macro to retrieve a
pointer to the private context structure. If diagnostics are being run on
_ChildHandle_, a produced protocol on _ChildHandle_ can be opened.

**********
**Note:** _If_ _ChildHandle_ _is_ **NULL**, _a request is made to run
diagnostics on the bus controller. If ChildHandle is not_ **NULL**_, a request
is made to run diagnostics on a child controller managed by the UEFI Driver._
**********

###### Example 149-RunDiagnostics() for a Bus Driver or Hybrid Driver

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
  // If ChildHandle is not NULL, then make sure this driver produced ChildHandle
  //
  if (ChildHandle != NULL) {
    Status = EfiTestChildHandle (
               ControllerHandle,
               ChildHandle,
               &gEfiPciIoProtocolGuid
               );
    if (EFI_ERROR (Status)) {
      return Status;
    }
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
  
  if (ChildHandle == NULL) {
    //
    // Perform Diagnostics Algorithm on the bus controller specified
    // by ControllerHandle for the type of diagnostics requested in
    // DiagnosticsType
    //
    // Return results in ErrorType, Buffer, and BufferSize
    //
    // If Diagnostics Algorithm fails, then return EFI_DEVICE_ERROR
    //
  } else {
    //
    // Perform Diagnostics Algorithm on child controller specified
    // by ChildHandle for the type of diagnostics requested in
    // DiagnosticsType
    //
    // Return results in ErrorType, Buffer, and BufferSize
    //
    // If Diagnostics Algorithm fails, then return EFI_DEVICE_ERROR
    //
  }
  
  return EFI_SUCCESS;
}
```

It is recommended that bus drivers and hybrid provide diagnostics for both the
bus controller and the child controllers produced by these driver types.
Implementing diagnostics for only the bus controller or only the child
controllers is strongly discouraged.
