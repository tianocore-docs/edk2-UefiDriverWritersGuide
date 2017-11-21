<!--- @file
  18.5.3 Bus Master Read and Write Operations

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

### 18.5.3 Bus Master Read and Write Operations

When a DMA transaction starts or stops, the ownership of the DMA buffer
transitions from the processor to the DMA bus master and back to the processor.
The PCI I/O Protocol provides the `Map()` and `Unmap()` services used to set up
and complete a DMA transaction.

The implementation of the PCI Root Bridge I/O Protocol uses the EDK II library
`BaseLib` function `MemoryFence()` to guarantee all system memory transactions
from the processor are completed before the DMA transaction is started. This
prevents a DMA bus master reading from a location in the DMA buffer before a
write transaction is flushed from the processor. Because this functionality is
built into the PCI Root Bridge I/O Protocol itself, the PCI driver writer need
not worry about bus master read/ write operations.

A PCI driver is responsible for flushing all posted write data from a PCI
controller when a bus master write operation is completed. First, the PCI
driver should read from a register on the PCI controller to guarantee that all
posted write operations are flushed from the PCI controller and through any
PCI-to-PCI bridges between the PCI controller and the PCI root bridge.

Because PCI drivers are polled, they typically read from a status register on
the PCI controller to determine when the bus master write transaction is
completed. This read operation is usually sufficient to flush the posted write
buffers. The PCI driver must also call the `PciIo->Flush()` service at the end
of a bus master write operation. This service flushes all the posted write
buffers in the system chipset and guarantees their commitment to system memory.
The combination of the read operation and the `PciIo>Flush()` call guarantee
that the bus master's view of system memory and the processor's view of system
memory are consistent.

An example of how a bus master write transaction should be completed to
guarantee the bus master's view of system memory is consistent with that of the
processor follows.

###### Example 177-Completing a bus master write operation

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>
#include <Library/UefiLib.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT64 Result64

//
// Call PollMem() to poll for Bit #0 in MMIO register 0x24 of Bar #1 to be set.
// This example shows polling a status register to wait for a bus master write
// transaction to complete.
//
Status = PciIo->PollMem (
                  PciIo,                         // This
                  EfiPciIoWidthUint32,           // Width
                  1,                             // BarIndex
                  0x24,                          // Offset 
                  BIT0,                          // Mask
                  BIT0,                          // Value
                  EFI_TIMER_PERIOD_SECONDS (1),  // Timeout
                  &Result64                      // Result
                  );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Call Flush() to flush all write transactions to system memory
//
Status = PciIo->Flush (PciIo);
if (EFI_ERROR (Status)) {
  return Status;
}
```
