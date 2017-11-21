<!--- @file
  18.6.2 PCI I/O FIFO operations

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

### 18.6.2 PCI I/O FIFO operations

The examples below show an example of writing a sector to an IDE controller.
The IDE controller uses a single 16-bit I/O port as a FIFO for reading and
writing sector data. The first example calls the PCI I/O Protocol 256 times to
write the sector. The second example calls the PCI I/O Protocol once to perform
the same operation, providing better performance if compiled with an EBC
compiler. This example applies equally to FIFO read operations.

###### Example 186-PCI I/O FIFO using a loop

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINTN Index;
UINT16 Buffer[256];

//
// This is the slowest method. It performs 256 PCI I/O calls to write 256
// 16-bit values to the IDE controller.
//
for (Index = 0; Index < 256; Index++) {
  Status = PciIo->Io.Write (
                     PciIo, // This
                     EfiPciIoWidthUint16,          // Width
                     EFI_PCI_IO_PASS_THROUGH_BAR,  // BarIndex
                     0x1F0,                        // Offset
                     1,                            // Count
                     &Buffer[Index]                // Buffer
                     );
}
```

###### Example 187-PCI I/O FIFO without a loop

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT16 Buffer[256];

//
// This is the fastest method. It uses a loop to write 256 16-bit values to
// the IDE controller.
//
Status = PciIo->Io.Write (
                     PciIo,                        // This
                     EfiPciIoWidthFifoUint16,      // Width
                     EFI_PCI_IO_PASS_THROUGH_BAR,  // BarIndex
                     0x1F0,                        // Offset
                     256,                          // Count
                     Buffer                        // Buffer
                     );
```
