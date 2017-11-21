<!--- @file
  18.5.1 Map() Service Cautions

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

### 18.5.1 Map() Service Cautions

A common mistake in writing PCI drivers is omission of the use of the `Map()`
service. On platforms with coherent PCI busses having a 1:1 mapping between CPU
addresses and PCI DMA addresses, such as PCI implementations on many IA32, X64,
and IPF systems, the omission of `Map()` may not produce any functional issues.
However, if those same UEFI Driver sources are used on a platform is that not
coherent, nor guarantees a 1:1 mapping between CPU addresses and PCI DMA
addresses, the UEFI Driver may not function correctly, with the likely result
being data corruption. For this reason, `Map()` must always be used when
setting up a PCI DMA transfer.

**********
**TIP:** Although omission of the `Map()` service may work on some platforms,
use of `Map()` for DMA transaction is required and maximizes UEFI Driver
compatibility.
**********

The `Map()` service converts a system memory address to an address useful to a
PCI device performing bus master DMA transactions. The device address returned
is not related to the original system memory address. Some chipsets maintain a
one-to-one mapping between system memory addresses and device addresses on the
PCI bus. For this special case, the system memory address and device address
are the same. However, a PCI driver cannot tell if it is executing on a
platform with this one-to-one mapping. As a result, a PCI driver must make as
few assumptions about the system architecture as possible. Avoiding assumptions
means that a PCI driver must never use the device address that is returned from
`Map()` to access the contents of the DMA buffer. Instead, this value should
only be used to program the base address of the DMA transaction into the PCI
controller. This programming is typically accomplished with one or more I/O or
memory-mapped I/O write transactions to the PCI controller the PCI driver is
managing.

The _example below_ shows the function prototype for the `Map()` service of the
PCI I/O Protocol. A PCI driver can use _HostAddress_ to access the contents of
the DMA buffer, but must never use the returned parameter _DeviceAddress_ to
access the contents of the DMA buffer.

###### Example 176-Map() Function

```c
/**
  Provides the PCI controller-specific addresses needed to access system memory.
  
  @param  This                    A pointer to the EFI_PCI_IO_PROTOCOL instance.
  @param  Operation               Indicates if the bus master is going to read or write to system memory.
  @param  HostAddress             The system memory address to map to the PCI controller.
  @param  NumberOfBytes           On input the number of bytes to map. On output the number of bytes that
                                  were mapped. @param DeviceAddress The resulting map address for the bus
                                  master PCI controller to use to access the hosts HostAddress. 
  @param Mapping                  A resulting value to pass to Unmap().
  @retval  EFI_SUCCESS            The range was mapped for the returned NumberOfBytes.
  @retval  EFI_UNSUPPORTED        The HostAddress cannot be mapped as a common buffer.
  @retval  EFI_INVALID_PARAMETER  One or more parameters are invalid.
  @retval  EFI_OUT_OF_RESOURCES   The request could not be completed due to a lack of resources.
  @retval  EFI_DEVICE_ERROR       The system hardware could not map the requested address.
**/

typedef
EFI_STATUS
(EFIAPI * EFI_PCI_IO_PROTOCOL_MAP)(
  IN EFI_PCI_IO_PROTOCOL              *This,
  IN EFI_PCI_IO_PROTOCOL_OPERATION    Operation,
  IN VOID                             *HostAddress,
  IN OUT UINTN                        *NumberOfBytes,
  OUT EFI_PHYSICAL_ADDRESS            *DeviceAddress,
  OUT VOID                            **Mapping
);
```
