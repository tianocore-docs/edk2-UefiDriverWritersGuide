<!--- @file
  18.5.8 DMA Bus Master Common Buffer Operation

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

### 18.5.8 DMA Bus Master Common Buffer Operation

A PCI driver uses common buffers when a memory region requires simultaneous
access by both the processor and a PCI bus master. A common buffer is typically
allocated in the `Start()` service and freed in the `Stop()` service. This
mechanism is very different from the bus master read and bus master write
operations where the PCI driver transfers the ownership of a memory region from
the processor to the bus master and back to the processor.

The general algorithm for allocating a common buffer in the `Start()` follows:

* Call `AllocateBuffer()` to allocate a common buffer.

* Call `Map()` with an _Operation_ of `EfiPciOperationBusMasterCommonBuffer`.

* Program the DMA bus master with the _DeviceAddress_ returned by `Map()`.

* The common buffer can now be accessed equally by the processor (using
  _HostAddress_) and the DMA bus master (using _DeviceAddress_) .

The general algorithm for freeing a common buffer in the `Stop()` service is as
follows:

* Call `Unmap()`. - Call `FreeBuffer()`.

The _example below_ shows an example function the `Start()` service calls to
set up a common buffer operation for a specific PCI controller. The function
accesses the PCI controller through the _PciIo_ parameter. The function
allocates a common buffer of _Length_ bytes and returns the address of the
common buffer in _HostAddress_.

A mapping is created for the common buffer and returned in the parameter
_Mapping_. The MMIO register at offset 0x18 of BAR #1 is the start address of
the common buffer from the PCI controller's perspective. The services of the
PCI I/O Protocol used in this example include `AllocateBuffer()`, `Map()`, and
`Mem.Write()`. This example is for a 32-bit PCI bus master. A 64-bit PCI bus
master requires two 32-bit MMIO registers to specify the start address, and the
`EFI_PCI_ATTRIBUTE_DUAL_ADDRESS_CYCLE` attribute must be set in the Driver
Binding Protocol `Start()` service of the PCI driver.

###### Example 180-Allocate bus master common buffer

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>

EFI_STATUS
EFIAPI
SetupCommonBuffer (
  IN  EFI_PCI_IO_PROTOCOL  *PciIo,
  IN  UINT8                **HostAddress,
  IN  UINTN                Length,
  OUT VOID                 **Mapping
  )
{
  EFI_STATUS               Status;
  UINTN                    NumberOfBytes;
  EFI_PHYSICAL_ADDRESS     DeviceAddress;
  UINT32                   DmaStartAddress;
  
  //
  // Allocate a common buffer from anywhere in system memory of
  // type EfiBootServicesData.
  //
  Status = PciIo->AllocateBuffer (
                    PciIo,                                  // This
                    AllocateAnyPages,                       // Type
                    EfiBootServicesData,                    // MemoryType EFI_SIZE_TO_PAGES 
                    (Length),                               // Pages
                    (VOID **)HostAddress,                   // HostAddress
                    0                                       // Attributes
                    );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // Call Map() to retrieve the DeviceAddress to use for the bus
  // master common buffer operation. If the Map() function cannot
  // support a DMA operation for the entire length, then return an
  // error.
  //
  NumberOfBytes = Length;
  Status = PciIo->Map (
                    PciIo,                                  // This
                    EfiPciIoOperationBusMasterCommonBuffer, // Operation
                    (VOID *)*HostAddress,                   // HostAddress
                    &NumberOfBytes,                         // NumberOfBytes
                    &DeviceAddress,                         // DeviceAddress
                    Mapping                                 // Mapping
                    );
  if (!EFI_ERROR (Status) && NumberOfBytes != Length) {
    PciIo->Unmap (PciIo, *Mapping);
    Status = EFI_OUT_OF_RESOURCES;
  }
  if (EFI_ERROR (Status)) {
    PciIo->FreeBuffer (
             PciIo,
             EFI_SIZE_TO_PAGES (Length),
             (VOID *)*HostAddress
             );
    return Status;
  }
  
  //
  // Write the DMA start address to MMIO Register offset 0x18 of Bar #1
  //
  DmaStartAddress = (UINT32)DeviceAddress;
  Status = PciIo->Mem.Write (
                        PciIo,                             // This
                        EfiPciIoWidthUint32,               // Width
                        1,                                 // BarIndex
                        0x18,                              // Offset
                        1,                                 // Count
                        &DmaStartAddress                   // Buffer
                        );
  if (EFI_ERROR (Status)) {
    PciIo->Unmap (PciIo, *Mapping);
    PciIo->FreeBuffer (
             PciIo,
             EFI_SIZE_TO_PAGES (Length),
             (VOID *)*HostAddress
             );
  }
  
  return Status;
}
```

This example shows a function the `Stop()` service calls to free a common
buffer for a PCI controller. The function accesses the PCI controller through
the services of the _PciIo_ parameter and uses them to free the common buffer
specified by _HostAddress_ and _Length_. This function undoes the mapping and
frees the common buffer. The services of the PCI I/O Protocol used in this
example include `Unmap()` and `FreeBuffer()`.

###### Example 181-Free bus master common buffer

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>

EFI_STATUS
EFIAPI
TearDownCommonBuffer (
  IN EFI_PCI_IO_PROTOCOL  *PciIo,
  IN UINT8                *HostAddress,
  IN UINTN                Length,
  IN VOID                 *Mapping
  )
{
  EFI_STATUS  Status;
  
  Status = PciIo->Unmap (PciIo, Mapping);
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  Status = PciIo->FreeBuffer (
                    PciIo,
                    EFI_SIZE_TO_PAGES (Length),
                    (VOID *)HostAddress
                    );
  return Status;
}
```
