<!--- @file
  18.6.6 PCI I/O Polling Operations

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

### 18.6.6 PCI I/O Polling Operations

These same types of optimization can be applied to polling as well. In the
_following_ _examples_, two different polling methods are shown:

* A loop with 10 μs stalls to wait up to 1 minute

* A single call to PCI I/O protocol to perform the entire operation

These types of polling operations are usually performed when a driver is
waiting for the hardware to complete an operation with the completion status
indicated by a bit changing state in an I/O port or a memory-mapped I/O port.
The examples below poll offset 0x20 in BAR #1 for bit 0 to change from 0 to 1.

The `PollIo()` and `PollMem()` functions in the PCI I/O Protocol are very
flexible and can simplify the operation of polling for bits to change state in
status registers.

###### Example 195-Using Mem.Read() and Stall() to poll for 1 second

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>
EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINTN TimeOut;
UINT8 Result8;
//
// Loop for up to 1 second waiting for Bit #0 in
// register 0x20 of BAR #1 to be set.
//
for (TimeOut = 0; TimeOut < 1000000; TimeOut += 10) {
  Status = PciIo->Mem.Read (
                        PciIo, // This
                        EfiPciIoWidthUint8, // Width
                        1, // BarIndex
                        0x20, // Offset
                        1, // Count
                        &Result8 // Value
                        );
  if ((Result8 & BIT0) == BIT0) {
    return EFI_SUCCESS;
  }
  gBS->Stall (10);
}
return EFI_TIMEOUT;
```

###### Example 196-Using PollIo() to poll for 1 second

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>
#include <Library/UefiLib.h>
EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT64 Result64;
//
// Call PollIo() to poll for Bit #0 in register 0x20 of Bar #1 to be set.
//
Status = PciIo->PollIo (
                  PciIo, // This
                  EfiPciIoWidthUint8, // Width
                  1, // BarIndex
                  0x20, // Offset
                  BIT0, // Mask
                  BIT0, // Value
                  EFI_TIMER_PERIOD_SECONDS (1), // Timeout
                  &Result64 // Result
                  );
```
