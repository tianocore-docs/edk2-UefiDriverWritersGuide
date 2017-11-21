<!--- @file
  31.5.1 POST Card Debug

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

### 31.5.1 POST Card Debug

A POST card is an add-in card that displays the hex value of an 8-bit I/O write
cycle to address 0x80 Some POST cards support more than 8-bits and use
additional I/O port addresses such as 0x81 The EDK II MdePkg provides a library
class called `PostCodeLib` that includes the `POST_CODE()` macro that may be
used to abstract access to a POST card. When a UEFI Driver is built, it can be
configured in the DSC file to map the `PostCodeLib` class to the
`MdePkg/Library/BasePostCodeLibPort80` instance that performs
8-bit writes to I/O port `0x80`. If a platform has the equivalent POST card
functionality, but it is not located at I/O port `0x80`, an alternate
implementation of the `PostCodeLib` instance can be provided that allows a UEFI
Driver to send POST code values to the alternate POST card device without any
source code changes to the UEFI Driver itself. This example shows an example
usage of the `POST_CODE()` macro to send POST code values of `0x10` and `0x11`
as a UEFI Driver enters and leaves the driver entry point.

###### Example 269-UEFI Driver Entry Point with POST_CODE() Macros

```c
#include <Uefi.h>
#include <Library/PostCodeLib.h>
#include <Library/UefiLib.h>

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  EFI_STATUS  Status;
  
  POST_CODE (0x10);
  
  //
  // Install driver model protocol(s) on ImageHandle
  //
  Status = EfiLibInstallDriverBinding (
             ImageHandle, // ImageHandle
             SystemTable, // SystemTable
             &gAbcDriverBinding, // DriverBinding
             ImageHandle // DriverBindingHandle
             );
  ASSERT_EFI_ERROR (Status);
  
  POST_CODE (0x11);
  
  return Status;
}
```

The `PostCodeLib` uses PCDs to enable and disable the `POST_CODE()` macros.
This means that `POST_CODE()` macros can be enabled during UEFI Driver
development and debug when a platform with a POST card is being used, and can
be easily disabled for production builds of UEFI Drivers. The example below
contains a portion of the DSC file that shows how to enable `POST_CODE()`
macros in a UEFI Driver.

###### Example 270-Enable POST_CODE() macros from DSC file

```ini
[PcdsFixedAtBuild]
  #
  # Set POST_CODE_PROPERTY_POST_CODE_ENABLED bit (0x8) in
  # PcdPostCodePropertyMask to enable POST_CODE() macros
  #
  gEfiMdePkgTokenSpaceGuid. PcdPostCodePropertyMask |0x08
```
