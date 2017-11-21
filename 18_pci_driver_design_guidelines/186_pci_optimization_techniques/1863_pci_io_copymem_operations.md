<!--- @file
  18.6.3 PCI I/O CopyMem() Operations

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

### 18.6.3 PCI I/O CopyMem() Operations

The following examples show how scrolling a frame buffer by different methods
can provide performance improvements. In the first, the scroll operation is
performed using a loop to move one scan line at a time. The PCI I/O Protocol
`CopyMem()` service is similar to the UEFI Boot Service `CopyMem()`, except the
PCI I/O Protocol operates on PCI MMIO ranges described by PCI MMIO BARs.

In general, the PCI I/O Protocol should be used, whenever possible, to
eliminate loops in the UEFI Driver. This example assumes a 1 MB frame buffer
MMIO, accessed through BAR #0 of the PCI graphics controller, with a screen 800
pixels wide, and 32 bits per pixel.

In the second example, the scroll operation is performed using a single PCI I/O
Protocol call to `CopyMem()` to produce the exact same result. The second
example executes significantly faster if the UEFI Driver is compiled with an
EBC compiler because the loop has been removed from the UEFI Driver.

###### Example 188-Scroll frame buffer using a loop

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINTN ScanLineWidth;
UINTN Index;
UINT32 Value;

//
// This is the slowest method that moves one pxiel at a time
// through the PCI I/O protocol.
//
ScanLineWidth = 800 * sizeof (UINT32);
for (Index = ScanLineWidth; Index < SIZE_1MB; Index += 4) {
  Status = PciIo->Mem.Read (
                        PciIo,                                   // This
                        EfiPciIoWidthUint32,                     // Width
                        0,                                       // BarIndex
                        Index,                                   // Offset
                        1,                                       // Count
                        &Value                                   // Buffer
                        );
  Status = PciIo->Mem.Write (
                        PciIo,                                   // This
                        EfiPciIoWidthUint32,                     // Width
                        0,                                       // Bar Index
                        Index - ScanLineWidth,                   // Offset
                        1,                                       // Count
                        &Value                                   // Buffer
                        );
}
```

###### Example 189-Scroll frame buffer without a loop

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINTN ScanLineWidth;

//
// This is the faster method that makes a single call to CopyMem().
//
ScanLineWidth = 800 * sizeof (UINT32);
Status = PciIo->CopyMem (
                  PciIo,                                        // This
                  EfiPciIoWidthUint32,                          // Width
                  0,                                            // DestBarIndex
                  0,                                            // DestOffset
                  0,                                            // SrcBarIndex
                  ScanLineWidth,                                // SrcOffset
                  (SIZE_1MB / sizeof (UINT32)) - ScanLineWidth  // Count
                  );
```
