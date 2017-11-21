<!--- @file
  5.2.8 CalculateCrc32()

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

### 5.2.8 `CalculateCrc32()`

Use this service to maintain the checksums in the UEFI System Table, UEFI boot
services table, and UEFI runtime services table. A UEFI driver that modifies
one of these tables should use this service to update the checksums. A UEFI
driver could compute the 32-bit CRC on its own, but the UEFI driver is smaller
if it takes advantage of this UEFI boot service. This service can also be used
to compute the checksums in Guided Partition Table(GPT) structures.

The following code fragment shows how `CalculateCrc32()` can be used to
calculate and update the 32-bit CRC field in the UEFI System Table header. The
EDK II library `UefiBootServicesTableLib` provides global variables for the
UEFI System Table, the UEFI Boot Services Table, and the Image Handle for the
currently executing driver. In this example, the global variable for the UEFI
System Table called `gST` and the global variable for the UEFI Boot Services
Table called `gBS` are used to reference the UEFI System Table header and call
the UEFI Boot Services `CalculateCrc32()`. Since the CRC32 field is part of the
structure for which the 32-bit CRC is being computed, it must be set to zero
before calling `CalculateCrc32()`.

###### Example 71-Calculate and update 32-bit CRC in UEFI System Table

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS Status;

gST->Hdr.CRC32 = 0;
Status = gBS->CalculateCrc32 (
                &gST->Hdr,
                gST->Hdr.HeaderSize,
                &gST->Hdr.CRC32
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

The code fragment below shows how to calculate a 32-bit CRC for an
`EXAMPLE_DEVICE` structure. Since the computed 32-bit CRC is not stored within
the `EXAMPLE_DEVICE` structure, it does not need to be zeroed before calling
the `CalculateCrc32()` service.

###### Example 72-Calculate and 32-bit CRC for a structure

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS Status;
EXAMPLE_DEVICE Device;
UINT32 Crc;

Status = gBS->CalculateCrc32 (&Device, sizeof (Device), &Crc);
if (EFI_ERROR (Status)) {
  return Status;
}
```

The `CalculateCrc32()` service can also be used to verify a 32-bit CRC value.
The code fragment below shows how the 32-bit CRC for the UEFI System Table
header can be verified. This algorithm preserves the original contents of the
UEFI System Table header. It returns `TRUE` if the 32-bit CRC is good.
Otherwise, it returns `FALSE`.

###### Example 73-Verify 32-bit CRC in UEFI System Table

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS Status;
UINT32 OriginalCrc32;
UINT32 Crc32;

OriginalCrc32 = gST->Hdr.CRC32;
gST->Hdr.CRC32 = 0;
Status = gBS->CalculateCrc32 (
                &gST->Hdr,
                gST->Hdr.HeaderSize,
                &Crc32
                );
gST->Hdr.CRC32 = OriginalCrc32;
if (EFI_ERROR (Status)) {
  return FALSE;
}
return (Crc32 == OriginalCrc32);
```
