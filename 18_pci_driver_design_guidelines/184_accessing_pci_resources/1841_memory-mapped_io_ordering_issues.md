<!--- @file
  18.4.1 Memory-mapped I/O ordering issues

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

### 18.4.1 Memory-mapped I/O ordering issues

PCI transactions follow the ordering rules defined in the _PCI Specification._
The ordering rules vary for I/O, memory-mapped I/O, and PCI configuration
cycles.

The PCI I/O Protocol `Mem.Read()` service generates PCI memory read cycles
guaranteed to complete before control is returned to the PCI driver. However,
the PCI I/O Protocol `Mem.Write()` service does not guarantee that PCI memory
cycles produced by this service are completed before control is returned to the
PCI driver. This distinction means that memory write transactions may be
sitting in write buffers when this service returns. If the PCI driver requires
a `Mem.Write()` transaction to complete, then the `Mem.Write()` transaction
must be followed by a `Mem.Read()` transaction to the same PCI controller. Some
chipsets and PCI-to-PCI bridges are more sensitive to this issue than others.

The following example shows a `Mem.Write()` call to a memory-mapped I/O
register at offset 0x20 into BAR #1 of a PCI controller. This write transaction
is followed by a `Mem.Read()` call from the same memory-mapped I/O register.
This combination guarantees that the write transaction is completed by the time
the `Mem.Read()` call returns.

In general, this mechanism is not required because a PCI driver typically reads
a status register and this read transaction forces all posted write
transactions to complete on the PCI controller. The only time to use this
mechanism is when a PCI driver performs a write transaction not immediately
followed by a read transaction and the PCI driver needs to guarantee that the
write transaction is completed immediately.

###### Example 173-Completing a memory write transaction

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT32 DmaStartAddress;

//
// Write the value in DmaStartAddress to offset 0x20 of BAR #1
//
Status = PciIo->Mem.Write (
                      PciIo,                // This
                      EfiPciIoWidthUint32,  // Width
                      1,                    // BarIndex
                      0x20,                 // Offset
                      1,                    // Count
                      &DmaStartAddress      // Buffer
                      );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Read offset 0x20 of BAR #1 This guarantees that the previous write
// transaction is posted to the PCI controller.
//
Status = PciIo->Mem.Read (
                      PciIo, // This
                      EfiPciIoWidthUint32,  // Width
                      1,                    // BarIndex
                      0x20,                 // Offset
                      1,                    // Count
                      &DmaStartAddress      // Buffer
                      );
if (EFI_ERROR (Status)) {
  return Status;
}
```
