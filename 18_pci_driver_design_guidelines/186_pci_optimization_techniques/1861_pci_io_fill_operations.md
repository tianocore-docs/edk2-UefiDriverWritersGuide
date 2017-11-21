<!--- @file
  18.6.1 PCI I/O fill operations

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

### 18.6.1 PCI I/O fill operations

The following examples show ways to fill video frame buffer with zeros on a PCI
video controller. The frame buffer is 1 MB of memory-mapped I/O accessed
through BAR #0 of the PCI video controller. The following four examples of
performing this operation are shown from slowest to fastest:

The following two methods can significantly increase performance of a UEFI
driver by taking advantage of the fill operations to eliminate loops and
writing to a PCI controller at the largest possible size.

###### Example 182-PCI I/O 8-bit fill with a loop

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT8 Color8;
UINTN Index;

//
// This is the slowest method. It performs SIZE_1MB calls through PCI I/O and
// writes to the frame buffer 8 bits at a time.
//
Color8 = 0;
for (Index = 0; Index < SIZE_1MB; Index++) {
  Status = PciIo->Mem.Write (
                        PciIo,                       // This
                        EfiPciIoWidthUint8,          // Width
                        0,                           // BarIndex
                        Index,                       // Offset
                        1,                           // Count
                        &Color8                      // Buffer
                        );
}
```

###### Example 183-PCI I/O 32-bit fill with a loop

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT32 Color32;
UINTN Index;

//
// This is the slowest method. It performs SIZE_1MB calls through PCI I/O and
// writes to the frame buffer 8 bits at a time.
//
Color32 = 0;
for (Index = 0; Index < SIZE_1MB; Index += 4) {
  Status = PciIo->Mem.Write (
                        PciIo,                       // This
                        EfiPciIoWidthUint32,         // Width
                        0,                           // BarIndex
                        Index,                       // Offset
                        1,                           // Count
                        &Color32                     // Buffer
                        );
}
```

###### Example 184-PCI I/O 8-bit fill without a loop

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT8 Color8;

//
// This is much better. It performs 1 call to PCI I/O, but it is writing the
// frame buffer 8 bits at a time.
//
Color8 = 0;
Status = PciIo->Mem.Write (
                      PciIo,                        // This
                      EfiPciIoWidthFillUint8,       // Width
                      0,                            // BarIndex
                      0,                            // Offset
                      SIZE_1MB,                     // Count
                      &Color8                       // Buffer
                      );
```

###### Example 185-PCI I/O 32-bit fill without a loop

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT32 Color32;

//
// This is the best method. It performs 1 call to PCI I/O, and it is writing
// the frame buffer 32 bits at a time.
//
Color32 = 0;
Status = PciIo->Mem.Write (
                      PciIo,                        // This
                      EfiPciIoWidthFillUint32,      // Width
                      0,                            // BarIndex
                      0,                            // Offset
                      SIZE_1MB / sizeof (UINT32),   // Count
                      &Color32                      // Buffer
                      );
```
