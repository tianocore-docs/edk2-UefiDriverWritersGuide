<!--- @file
  18.5.6 DMA Bus Master Read Operation

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

### 18.5.6 DMA Bus Master Read Operation

The general algorithm for performing a bus master read operation is as follows:

* The processor initializes the contents of the DMA using _HostAddress_.

* Call `Map()` with an _Operation_ of `EfiPciOperationBusMasterRead`.

* Program the DMA bus master with the _DeviceAddress_ returned by `Map()`.

* Program the DMA bus master with the _NumberOfBytes_ returned by `Map()`.

* Start the DMA bus master.

* Wait for DMA bus master to complete the bus master read operation.

* Call `Unmap()`.

The following example shows a function for performing a bus master read
operation on a PCI controller. The PCI controller is accessed through the
parameter _PciIo_. The system memory buffer read by the bus master is specified
by _HostAddress_ and _Length_. This function performs one or more bus master
read operations until either _Length_ bytes have been read by the bus master or
an error is detected. The PCI controller in this example has three MMIO
registers in BAR #1 The MMIO register at offset 0x10 is a status register the
function uses to check if the DMA operation is complete or not. The function
writes the start of the DMA transaction to the MMIO register at offset 0x20 and
the length of the DMA transaction to the MMIO register at offset 0x24 The write
operation to offset 0x24 also starts the DMA read operation. The services of
the PCI I/O Protocol used in this example include `Map()`, `Unmap()`,
`Mem.Write()`, and `PollMem()`. The example below is for a 32-bit PCI bus
master.

A 64-bit PCI bus master instance uses two 32-bit MMIO registers to specify the
start address and two 32-bit MMIO registers to specify the length. If the PCI
bus master supports 64-bit DMA addressing, the
`EFI_PCI_ATTRIBUTE_DUAL_ADDRESS_CYCLE` attribute must be set in the Driver
Binding Protocol `Start()` service of the PCI driver.

###### Example 178-Bus master read operation

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>
#include <Library/UefiLib.h>

EFI_STATUS
EFIAPI
DoBusMasterRead (
  IN EFI_PCI_IO_PROTOCOL  *PciIo,
  IN UINT8                *HostAddress,
  IN UINTN                *Length
  )
{
  EFI_STATUS              Status;
  UINTN                   NumberOfBytes;
  EFI_PHYSICAL_ADDRESS    DeviceAddress;
  VOID                    *Mapping;
  UINT32                  DmaStartAddress;
  UINT64                  ControllerStatus;
  
  //
  // Loop until the entire buffer specified by HostAddress and
  // Length has been read from the PCI DMA bus master
  // do {
    //
    // Call Map() to retrieve the DeviceAddress to use for the bus
    // master read operation. The Map() function may not support
    // performing a DMA operation for the entire length, so it may
    // be broken up into smaller DMA operations.
    //
    NumberOfBytes = *Length;
    Status = PciIo->Map (
                    PciIo, // This
                    EfiPciIoOperationBusMasterRead, // Operation
                    (VOID *)HostAddress,            // HostAddress
                    &NumberOfBytes,                 // NumberOfBytes
                    &DeviceAddress,                 // DeviceAddress
                    &Mapping                        // Mapping
                    );
    if (EFI_ERROR (Status)) {
      return Status;
    }
  
    //
    // Write the DMA start address to MMIO Register 0x20 of Bar #1
    //
    DmaStartAddress = (UINT32)DeviceAddress;
    Status = PciIo->Mem.Write (
                        PciIo,                      // This
                        EfiPciIoWidthUint32,        // Width
                        1,                          // BarIndex
                        0x20,                       // Offset
                        1,                          // Count
                        &DmaStartAddress            // Buffer
                        );
    if (EFI_ERROR (Status)) {
      return Status;
    }
  
    //
    // Write the length of the DMA to MMIO Register 0x24 of Bar #1
    // This write operation also starts the DMA transaction
    //
    Status = PciIo->Mem.Write (
                        PciIo, // This
                        EfiPciIoWidthUint32,        // Width
                        1,                          // BarIndex
                        0x24,                       // Offset
                        1,                          // Count
                        &NumberOfBytes              // Buffer
                        );
    if (EFI_ERROR (Status)) {
      return Status;
    }
  
    //
    // Call PollMem() to poll for Bit #0 in MMIO register 0x10 of
    // Bar #1
    //
    Status = PciIo->PollMem (
                    PciIo,                          // This
                    EfiPciIoWidthUint32,            // Width
                    1,                              // BarIndex
                    0x10,                           // Offset
                    BIT0,                           // Mask
                    BIT0,                           // Value
                    EFI_TIMER_PERIOD_SECONDS (1),   // Timeout
                    &ControllerStatus // Result
                    );
    if (EFI_ERROR (Status)) {
      return Status;
    }
  
    //
    // Call Unmap() to complete the bus master read operation
    //
    Status = PciIo->Unmap (PciIo, Mapping);
    if (EFI_ERROR (Status)) {
    return Status;
    }
  
    //
    // Update the HostAddress and Length remaining based upon the
    // number of bytes transferred
    //
    HostAddress += NumberOfBytes;
    *Length -= NumberOfBytes;
  } while (*Length != 0);
  return Status;
}
```
