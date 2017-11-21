<!--- @file
  4.1.1 Type Checking

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

### 4.1.1 Type Checking

Some compilers perform stronger type checking than other compilers such as the
Intel family of compilers including the Intel(R) C Compiler for EFI Byte Code.
As a result, code that compiles without any errors or warnings on one compiler
may generate warnings or errors when compiled with another compiler. The
following example shows two common examples from UEFI Drivers that use
`AllocatePool()` and `OpenProtocol()`.

These examples show the style that may generate warnings with some compilers,
and the correct method to prevent the warnings.

###### Example 7-Stronger type checking

```c
#include <Uefi.h>
#include <Protocol/BlockIo.h>
#include <Protocol/DriverBinding.h>
#include <Library/UefiBootServicesTableLib.h>
typedef struct {
  UINT8 First;
  UINT32 Second;
} MY_STRUCTURE;
EFI_STATUS Status;
EFI_DRIVER_BINDING_PROTOCOL *This;
EFI_HANDLE ControllerHandle;
EFI_BLOCK_IO_PROTOCOL *BlockIo;
MY_STRUCTURE *MyStructure;
Status = gBS->OpenProtocol (
                ControllerHandle,
                &gEfiBlockIoProtocolGuid,
                &BlockIo, // Compiler warning
                This->DriverBindingHandle,
                ControllerHandle,
                EFI_OPEN_PROTOCOL_BY_DRIVER
                );
Status = gBS->OpenProtocol (
                ControllerHandle,
                &gEfiBlockIoProtocolGuid,
                (VOID **)&BlockIo, // No compiler warning
                This->DriverBindingHandle,
                ControllerHandle,
                EFI_OPEN_PROTOCOL_BY_DRIVER
                );
Status = gBS->AllocatePool (
                EfiBootServicesData,
                sizeof (MY_STRUCTURE),
                &MyStructure // Compiler warning
                );
Status = gBS->AllocatePool (
                EfiBootServicesData,
                sizeof (MY_STRUCTURE),
                (VOID **)&MyStructure // No compiler warning
                );
```
