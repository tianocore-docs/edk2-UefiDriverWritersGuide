<!--- @file
  5.3.8 GetNextVariableName()

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

### 5.3.8 GetNextVariableName()

This service is used to traverse the list of UEFI variables that are maintained
through the UEFI Variable Services. Since, in general, UEFI drivers know the
specific UEFI variables that the UEFI Driver is required to access, there is no
need for a UEFI driver to traverse the list of all the UEFI variables. This
service is typically used by UEFI applications, such as a diagnostic or a debug
utility, to show the contents of all the UEFI Variables present in a platform.

The example below shows how the `GetNextVariableName()` service can be used to
traverse and print the entire contents of the UEFI variable store. It uses the
EDK II `MemoryAllocationLib` to allocate, reallocate, and free buffers; the EDK
II `UefiLib` to print formatted strings to the UEFI console output device; and
the EDK II `UefiRuntimeServicesTableLib` to call the `GetNextVariableName()` and
`GetVariable()` runtime services.

###### Example 83-Print all UEFI variable store contents

```c
#include <Uefi.h>
#include <Library/UefiLib.h>
#include <Library/UefiRuntimeServicesTableLib.h>
#include <Library/MemoryAllocationLib.h>

EFI_STATUS Status;
EFI_GUID Guid;
UINTN NameBufferSize;
UINTN NameSize;
CHAR16 *Name;
UINTN DataSize;
UINT8 *Data;
UINTN Index;

//
// Initialize the variable name and data buffer variables
// to retrieve the first variable name in the variable store
//
NameBufferSize = sizeof (CHAR16);
Name = AllocateZeroPool (NameBufferSize);

//
// Loop through all variables in the variable store
//
while (TRUE) {
  //
  // Loop until a large a large enough variable name buffer is allocated
  // do {
  NameSize = NameBufferSize;
  Status = gRT->GetNextVariableName (&NameSize, Name, &Guid);
  if (Status == EFI_BUFFER_TOO_SMALL) {
    //
    // Grow the buffer Name to NameSize bytes
    //
    Name = ReallocatePool (NameBufferSize, NameSize, Name);
    if (Name == NULL) {
      return EFI_OUT_OF_RESOURCES;
    }
    NameBufferSize = NameSize;
  }
}
while (Status == EFI_BUFFER_TOO_SMALL);

//
// Exit main loop after last variable name is retrieved
//
if (EFI_ERROR (Status)) {
  FreePool (Name);
  return Status;
}

//
// Print variable guid and name
//
Print (L"%g : %s", &Guid, Name);

//
// Initialize variable data buffer as an empty buffer
//
DataSize = 0;
Data = NULL;

//
// Loop until a large enough variable data buffer is allocated
//
do {
  Status = gRT->GetVariable (Name, &Guid, NULL, &DataSize, Data);
  if (Status == EFI_BUFFER_TOO_SMALL) {
    //
    // Allocate new buffer for the variable data
    //
    Data = AllocatePool (DataSize);
    if (Data == NULL) {
      FreePool (Name);
      return EFI_OUT_OF_RESOURCES;
    }
  }
} while (Status == EFI_BUFFER_TOO_SMALL);
if (EFI_ERROR (Status)) {
  FreePool (Data);
  FreePool (Name);
  return Status;
}

//
// Print variable data
//
for (Index = 0; Index < DataSize; Index++) {
  if ((Index & 0x0f) == 0) {
    Print (L"\n ");
  }
  Print (L"%02x ", Data[Index]);
}
Print (L"\n");
FreePool (Data);
}
```
