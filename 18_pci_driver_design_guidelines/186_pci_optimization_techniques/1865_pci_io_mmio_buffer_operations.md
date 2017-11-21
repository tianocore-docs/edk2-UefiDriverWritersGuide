<!--- @file
  18.6.5 PCI I/O MMIO Buffer Operations

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

### 18.6.5 PCI I/O MMIO Buffer Operations

The following examples demonstrate how writing to a PCI memory-mapped I/O
buffer can dramatically affect the performance of a UEFI Driver. In the first
example, a loop is used with 8-bit operations. In the second, the same
operation is done with a single call. This example is based on writing to a 1MB
frame buffer by a UEFI Driver for a graphics controller.

**********
**Note:** The examples shown here apply equally well to reading a bitmap from
the frame buffer of a PCI video controller using the `PciIo->Mem.Read()`
function.
**********

###### Example 193-Write 1MB Frame Buffer using a loop

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>
EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT8 gBitMap[SIZE_1MB];
UINTN Index;
//
// Loop writing a 1 MB bitmap to the frame buffer 8 bits at a time.
//
for (Index = 0; Index < sizeof (gBitMap); Index++) {
  Status = PciIo->Mem.Write (
                        PciIo, // This
                        EfiPciIoWidthUint8, // Width
                        0, // BarIndex
                        Index, // Offset
                        1, // Count
                        &gBitMap[Index] // Buffer
                        );
}
```

###### Example 194-Write 1MB Frame Buffer with no loop

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>
EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT8 gBitMap[SIZE_1MB];
//
// Faster method that removes the loop and writes 32 bits at a time.
//
Status = PciIo->Mem.Write (
                      PciIo, // This
                      EfiPciIoWidthUint32, // Width
                      0, // BarIndex
                      0,
                      // Offset sizeof (gBitMap) / sizeof (UINT32),
                      // Count gBitMap
                      // Buffer
                      );
```
