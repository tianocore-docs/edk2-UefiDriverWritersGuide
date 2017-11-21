<!--- @file
  18.3.2 Start() and Stop()

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

### 18.3.2 Start() and Stop()

The `Start()` service of the Driver Binding Protocol for a PCI driver also
opens the PCI I/O Protocol with an attribute of `EFI_OPEN_PROTOCOL_BY_DRIVER`.
If the PCI driver is a bus or hybrid driver, the Device Path Protocol opens
using the attribute `EFI_OPEN_PROTOCOL_BY_DRIVER`. A device driver is not required to open the
Device Path Protocol. In addition, all PCI drivers are required to call the `Attributes()`
service of the PCI I/O Protocol to enable the I/O, memory, and bus master bits
in the Command register of the PCI configuration header. By default, the PCI
bus driver is not required to enable the Command register of the PCI
controllers. Instead, it is the responsibility of the `Start()` service to
enable these bits and that of the `Stop()` service to restore these bits. In
order for the `Stop()` service to restore the attributes, a PCI Driver
typically stores the original attributes in a `UINT64` field of the private
context data structure.

There is one additional attribute that must be specified in this call to the
`Attributes()` service. If the PCI controller is a bus master and capable of
generating 64-bit DMA addresses, the `EFI_PCI_IO_ATTRIBUTE_DUAL_ADDRESS_CYCLE`
attribute must also be enabled. Unfortunately, there is no standard method for
detecting if a PCI controller supports 32-bit or 64-bit DMA addresses. As a
result, it is the PCI driver's responsibility to inform the PCI bus driver that
the PCI controller is capable of producing 64-bit DMA addresses.

The PCI bus driver assumes that all PCI controllers are only capable of
generating 32-bit DMA addresses unless the PCI driver enables the dual address cycle
attribute.

The PCI bus driver uses this information along with the services of the PCI
Root Bridge I/O Protocol to perform PCI DMA transactions. If a PCI bus master that is
capable of 32-bit DMA addresses is present in a platform supporting more than 4
GB of system memory, the DMA transactions may have to be double buffered.
Double buffering can reduce the performance of a driver. It is also possible
for some platforms to only support system memory above 4 GB. For these reasons,
a PCI driver must always accurately describe the DMA capabilities of the PCI
controller from the `Start()` service of the Driver Binding Protocol.

The example below shows the code fragment from the `Start()`services of a PCI
driver for a PCI controller supporting 64-bit DMA transactions. The example
opens the PCI I/O Protocol attribute of `EFI_OPEN_PROTOCOL_BY_DRIVER`. It then
retrieves the current set of PCI I/O Protocol attributes and saves them in the
private context data structure field called `ABC_PRIVATE_DATA`. It then
determines what attribute the PCI I/O Protocol supports and enables the I/O
decode, MMIO decode, and Bus Master, and Dual Address Cycle capabilities. If a
PCI Controller does not support DAC, the only change is the removal of
`EFI_PCI_IO_ATTRIBUTE_DUAL_ADDRESS_CYCLE` from the last call to the
`Attributes()` service of the PCI I/O Protocol.

###### Example 171-Start() for a 64-bit DMA-capable PCI controller

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/PciIo.h>
#include <Library/UefiBootServicesTableLib.h>

typedef struct {
  UINTN Signature;
  //
  // . .
  //
  UINT64 OriginalPciAttributes;
} ABC_PRIVATE_DATA;

EFI_STATUS Status;
EFI_DRIVER_BINDING_PROTOCOL *This;
EFI_HANDLE ControllerHandle;
ABC_PRIVATE_DATA *Private;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT64 PciSupports;

//
// Open the PCI I/O Protocol
//
Status = gBS->OpenProtocol (
                ControllerHandle,
                &gEfiPciIoProtocolGuid,
                (VOID **)&PciIo,
                This->DriverBindingHandle,
                ControllerHandle,
                EFI_OPEN_PROTOCOL_BY_DRIVER
                );
if (EFI_ERROR (Status)) {
  goto Done;
}

//
// Retrieve original PCI attributes and save them in the private context data
// structure.
//
Status = PciIo->Attributes (
                  PciIo,
                  EfiPciIoAttributeOperationGet,
                  0,
                  &Private->OriginalPciAttributes
                  );
if (EFI_ERROR (Status)) {
  goto Done;
}

//
// Retrieve attributes that the PCI Controller supports
//
Status = PciIo->Attributes (
                  PciIo,
                  EfiPciIoAttributeOperationSupported,
                  0,
                  &PciSupports
                  );
if (EFI_ERROR (Status)) {
  goto Done;
}

//
// Enable Command register and Dual Address Cycle
//
Status = PciIo->Attributes (
                  PciIo,
                  EfiPciIoAttributeOperationEnable,
                  (PciSupports & EFI_PCI_DEVICE_ENABLE) |
                  EFI_PCI_IO_ATTRIBUTE_DUAL_ADDRESS_CYCLE,
                  NULL
                  );
if (EFI_ERROR (Status)) {
  goto Done;
}
```

This example shows the code fragment from the `Stop()`services of a PCI driver.
This example restores the PCI I/O Protocol attributes from a field of the
private context data structure called `ABC_PRIVATE_DATA`.

###### Example 172-Restore PCI Attributes in Stop()

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
ABC_PRIVATE_DATA *Private;
//
// Restore original PCI attributes
//
PciIo->Attributes (
         PciIo,
         EfiPciIoAttributeOperationSet,
         Private->OriginalPciAttributes,
         NULL
         );
//
// Close the PCI I/O Protocol
//
gBS->CloseProtocol (
       ControllerHandle,
       &gEfiPciIoProtocolGuid,
       This->DriverBindingHandle,
       ControllerHandle
       );
```

The following table lists the `#define` statements compatible with the
`Attributes()` service. A PCI driver must use the `Attributes()` service to
enable the decodes on the PCI controller, accurately describe the PCI
controller DMA capabilities, and request that specific I/O cycles are forwarded
to the device. The call to `Attributes()` fails if the request cannot be
satisfied. If this failure occurs, the `Start()` function must return an error.

Once again, any attributes enabled in the `Start()` service must be restored in
the `Stop()` service.

###### Table 26-PCI Attributes

| **Attribute**                             | **Description**                                                                            |
| ----------------------------------------- | ------------------------------------------------------------------------------------------ |
| `EFI_PCI_IO_ATTRIBUTE_ISA_MOTHERBOARD_IO` | Used to request the forwarding of I/O cycles 0x0000-0x00FF (10-bit decode).                |
| `EFI_PCI_IO_ATTRIBUTE_ISA_IO`             | Used to request the forwarding of I/O cycles 0x100-0x3FF (10-bit decode).                  |
| `EFI_PCI_IO_ATTRIBUTE_VGA_PALETTE_IO`     | Used to request the forwarding of I/O cycles 0x3C6, 0x3C8, and 0x3C9 (10-bit decode).      |
| `EFI_PCI_IO_ATTRIBUTE_VGA_MEMORY`         | Used to request the forwarding of MMIO cycles 0xA0000-0xBFFFF (24-bit decode).             |
| `EFI_PCI_IO_ATTRIBUTE_VGA_IO`             | Used to request the forwarding of I/O cycles 0x3B0-0x3BB and 0x3C0- 0x3DF (10-bit decode). |
| `EFI_PCI_IO_ATTRIBUTE_IDE_PRIMARY_IO`     | Used to request the forwarding of I/O cycles 0x1F0-0x1F7, 0x3F6, 0x3F7 (10-bit decode).    |
| `EFI_PCI_IO_ATTRIBUTE_IDE_SECONDARY_IO`   | Used to request the forwarding of I/O cycles 0x170-0x177, 0x376, 0x377 (10-bit decode).    |
| `EFI_PCI_IO_ATTRIBUTE_IO`                 | Enable the I/O decode bit in the Command register.                                         |
| `EFI_PCI_IO_ATTRIBUTE_MEMORY`             | Enable the Memory decode bit in the Command register.                                      |
| `EFI_PCI_IO_ATTRIBUTE_BUS_MASTER`         | Enable the Bus Master bit in the Command register.                                         |
| `EFI_PCI_IO_ATTRIBUTE_DUAL_ADDRESS_CYCLE` | Clear for PCI controllers that cannot generate a DAC.                                      |
| `EFI_PCI_IO_ATTRIBUTE_ISA_IO_16`          | Used to request the forwarding of I/O cycles 0x100-0x3FF (16-bit decode).                  |
| `EFI_PCI_IO_ATTRIBUTE_VGA_PALETTE_IO_16`  | Used to request the forwarding of I/O cycles 0x3C6, 0x3C8, and 0x3C9 (16-bit decode).      |
| `EFI_PCI_IO_ATTRIBUTE_VGA_IO_16`          | Used to request the forwarding of I/O cycles 0x3B0-0x3BB and 0x3C0- 0x3DF (16-bit decode). |

The table below, lists `#define` statements _not_ part of the _UEFI
Specification_, but which _are included_ in EDK II to simplify PCI driver
implementations. These attributes cover the typical classes of hardware
capabilities and provide a names for common combinations of attributes
described in the PCI Bus Support chapter of the _UEFI Specification_.

**********
**TIP:** For code readability, the Enable attributes included in EDK II should
be used.
**********

###### Table 27-EDK II attributes #defines

| **Attribute**           | **Description**                                                       |
| ----------------------- | --------------------------------------------------------------------- |
| `EFI_PCI_DEVICE_ENABLE` | Equivalent to a logical OR combination of `EFI_PCI_IO_ATTRIBUTE_IO`, 'EFI_PCI_IO_ATTRIBUTE_MEMORY`, and `EFI_PCI_IO_ATTRIBUTE_BUS_MASTER`. |
| `EFI_VGA_DEVICE_ENABLE` | Equivalent to a logical OR combination of `EFI_PCI_IO_ATTRIBUTE_VGA_PALETTE_IO`, `EFI_PCI_IO_ATTRIBUTE_VGA_MEMORY`, `EFI_PCI_IO_ATTRIBUTE_VGA_IO`, and   `EFI_PCI_IO_ATTRIBUTE_IO`. |

This table lists the `#define` statements that to use with the
`GetBarAttributes()` and `SetBarAttributes()` services to adjust the attributes
of a memory-mapped I/O region described by a Base Address Register (BAR) of a
PCI controller. The support of these attributes is optional, but in general, a
PCI driver uses these attributes to provide hints that may be used to improve
the performance of a PCI driver. Improved performance is especially important
for PCI drivers managing graphics controllers. Do note that any BAR attributes
set in the `Start()` service must be restored in the `Stop()` service.

###### Table 28-PCI BAR attributes

| **Attribute** | **Description**                         |
| ------------- | --------------------------------------- |
| `EFI_PCI_IO_ATTRIBUTE_MEMORY_WRITE_COMBINE` | Setting this bit enables platform support for memory range access in a write-combining mode. It improves write performance to a memory buffer on a PCI controller. By default, PCI memory ranges are not accessed in a write combining mode. |
| `EFI_PCI_IO_ATTRIBUTE_MEMORY_CACHED`        | Setting this bit enables platform support for changing the attributes of a PCI memory range so that it is accessed in a cached mode. By default, PCI memory ranges are not cached.                                                           |
| `EFI_PCI_IO_ATTRIBUTE_MEMORY_DISABLE`       | Setting this bit enables platform support for disabling a PCI memory range so that it can no longer be accessed. By default, all PCI memory ranges are enabled.                                                                              |

Sometimes there may be different logic paths in a UEFI Driver between a PCI
add-in card and a PCI controller integrated into a platform. The PCI I/O
Protocol provides attributes that help a UEFI Driver determine if a specific
PCI Controller and its associated PCI Option ROM image are from a PCI add-in
card in a PCI slot or if they are integrated into a platform. The attributes
shown in the following table list the `#define` statements for these
attributes. These attributes are read-only and the values are established by
the PCI Bus Driver when a PCI Controller is discovered and the PCI I/O Protocol
is produced. A PCI driver may retrieve the attributes of a PCI controller with
the `Attributes()` service of the PCI I/O Protocol, but a PCI Driver is not
allowed to modify these attributes.

###### Table 29-PCI Embedded Device Attributes

| **Attribute** | **Description**                            |
| ------------- | ------------------------------------------ |
| `EFI_PCI_IO_ATTRIBUTE_EMBEDDED_DEVICE` | If this bit is set, the PCI controller is an embedded device; typically a component on the system board. If this bit is clear, the PCI controller is part of an adapter populating one of the systems PCI slots.                                                                           |
| `EFI_PCI_IO_ATTRIBUTE_EMBEDDED_ROM`    | If this bit is set, the PCI option ROM described by the _RomImage_ and _RomSize_ fields is not from ROM BAR of the PCI controller. If this bit is clear, the _RomImage_ and _RomSize_ fields were initialized based on the PCI option ROM found through the ROM BAR of the PCI controller. |
