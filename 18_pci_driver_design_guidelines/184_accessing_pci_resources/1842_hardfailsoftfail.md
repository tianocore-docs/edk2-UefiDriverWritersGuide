<!--- @file
  18.4.2 Hardfail/Softfail

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

### 18.4.2 Hardfail/Softfail

PCI drivers must make sure they do not access resources not allocated to any
PCI controllers. Doing so may produce unpredictable results including platform
hang conditions.

For example, if a VGA device is in monochrome mode, accessing the VGA device's
color registers may cause unpredictable results. The best rule of thumb here is
to access only I/O or memory-mapped I/O resources to which the PCI driver
knows, for sure, that the PCI controller does respond. In general, this is not
a concern because the PCI I/O Protocol services do not allow the PCI driver to
access resources outside the resource ranges described in the BARs of the PCI
controllers. However, two mechanisms allow a PCI driver to bypass these
safeguards.

* The first is to use the `EFI_PCI_IO_PASS_THROUGH_BAR` with the PCI I/O
  Protocol services providing access to I/O and memory-mapped I/O regions.

* The second is for a PCI driver to retrieve and use the services of a PCI Root
  Bridge I/O Protocol.

A PCI driver uses the `EFI_PCI_IO_PASS_THROUGH_BAR` to access ISA resources on
a PCI controller. For a PCI driver to use this mechanism safely, the PCI driver
must know that the desired PCI controller does respond to the I/O or
memory-mapped I/O requests in the ISA ranges. The PCI driver can typically know
if it responds by examining the class code, vendor ID, and device ID fields of
the PCI controller in the PCI configuration header. The PCI driver must examine
the PCI configuration header before any I/O or memory-mapped I/O operations are
generated. The PCI configuration header is typically examined in the
`Supported()` service, so it is safe to access the ISA resources in the
`Start()` service and in the services of the I/O abstraction that the PCI
driver is producing. The following is an example using the
`EFI_PCI_IO_PASS_THROUGH_BAR`.

###### Example 174-Accessing ISA resources on a PCI controller

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT8 Data;
UINT16 Word;

//
// Write 0xAA to a Post Card at ISA address 0x80
//
Data = 0xAA;
Status = PciIo->Io.Write (
                     PciIo,                        // This
                     EfiPciIoWidthUint8,           // Width
                     EFI_PCI_IO_PASS_THROUGH_BAR,  // BarIndex
                     0x80,                         // Offset
                     1,                            // Count
                     &Data                         // Buffer
                     );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Read the first word from the VGA frame buffer
//
Status = PciIo->Mem.Read (
                      PciIo,                       // This
                      EfiPciIoWidthUint16,         // Width
                      EFI_PCI_IO_PASS_THROUGH_BAR, // BarIndex
                      0xA0000, // Offset
                      1, // Count
                      &Word // Buffer
                      );
if (EFI_ERROR (Status)) {
  return Status;
}
```

A PCI driver must also take care when using the services of the PCI Root Bridge
I/O Protocol. It can retrieve the parent PCI Root Bridge I/O Protocol and use
those services to touch any resource on the PCI bus.

**********
**Caution:** This touching of resources on the PCI bus can be very dangerous
because the PCI driver may not know if a different PCI driver owns a resource
or not. The use of this mechanism is **strongly discouraged** and is best left
to OEM drivers having intimate knowledge of the platform and chipset.
**********

_Chapter 5_ discusses the use of the `LocateDevicePath()` service and the
example associated with this service shows how the parent PCI Root Bridge I/O
Protocol can be retrieved.

Instead of using the parent PCI Root Bridge I/O Protocol, PCI drivers needing
access to the resources of other PCI controllers in the platform should search
the Handle Database for controller handles supporting the PCI I/O Protocol. To
prevent resource conflicts, open PCI I/O Protocols from other PCI controllers
with `EFI_OPEN_PROTOCOL_BY_DRIVER`.

The following example shows how a PCI driver can easily retrieve the list of
PCI controller handles in the Handle Database and use the services of the PCI
I/O Protocol on each of those handles to find peer PCI controllers.

For example, a PCI adapter containing multiple PCI controllers behind a
PCI-to-PCI bridge may use a single driver to manage all of the controllers on
the adapter. When the PCI driver is connected to the first PCI controller on
the adapter, the PCI driver connects to all the other PCI controllers having
the same bus number as the first. This example takes advantage of the
`GetLocation()` service of the PCI I/O Protocol to find matching bus numbers.

###### Example 175-Locate PCI handles with matching bus number

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>
#include <Library/MemoryAllocationLib.h>

EFI_STATUS           Status;
EFI_PCI_IO_PROTOCOL  *PciIo;
UINTN                HandleCount;
EFI_HANDLE           *HandleBuffer;
UINTN                Index;
UINTN                MyBus;
UINTN                Seg;
UINTN                Bus;
UINTN                Device;
UINTN                Function;

//
// Retrieve the location of the PCI controller and store the bus
// number in MyBus.
//
Status = PciIo->GetLocation (PciIo, &Seg, &MyBus, &Device, &Function);
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Retrieve the list of handles that support the PCI I/O protocol
// from the handle database. The number of handles that support
// the PCI I/O Protocol is returned in HandleCount, and the array
// of handle values is returned in HandleBuffer.
//
Status = gBS->LocateHandleBuffer (
                ByProtocol,
                &gEfiPciIoProtocolGuid,
                NULL,
                &HandleCount,
                &HandleBuffer
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Loop through all the handles the support the PCI I/O Protocol,
// and retrieve the instance of the PCI I/O Protocol. Use the
// EFI_OPEN_PROTOCOL_BY_DRIVER open mode, so only PCI I/O Protocols
// that are not currently being managed are considered.
//
for (Index = 0; Index < HandleCount; Index++) {
  Status = gBS->OpenProtocol (
                  HandleBuffer[Index],
                  &gEfiPciIoProtocolGuid,
                  (VOID **)&PciIo,
                  gImageHandle,
                  NULL,
                  EFI_OPEN_PROTOCOL_BY_DRIVER
                  );
  if (EFI_ERROR (Status)) {
    continue;
  }

  //
  // Retrieve the location of the PCI controller and store the
  // bus number in Bus.
  //
  Status = PciIo->GetLocation (PciIo, &Seg, &Bus, &Device, &Function);
  if (EFI_ERROR (Status) && Bus != MyBus) {
    //
    // Either the handle was already opened by another driver or the
    // bus numbers did not match, so close the PCI I/O Protocol and
    // move on to the next PCI handle.
    //
    gBS->CloseProtocol (
           HandleBuffer[Index],
           &gEfiPciIoProtocolGuid,
           gImageHandle,
           NULL
           );
    continue;
  }

  //
  // Store HandleBuffer[Index] so the driver knows it is managing the PCI
  // controller represented by HandleBuffer[Index]. This would typically be
  // stored in the private context data structure
  //
}

//
// Free the array of handles that was allocated by gBS->LocateHandleBuffer()
//
FreePool (HandleBuffer);
```
