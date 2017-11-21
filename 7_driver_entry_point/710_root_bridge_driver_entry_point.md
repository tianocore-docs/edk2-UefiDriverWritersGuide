<!--- @file
  7.10 Root bridge driver entry point

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

## 7.10 Root bridge driver entry point

Root bridge drivers produce handles and software abstractions for the bus types
directly produced by a core chipset. The PCI Root Bridge I/O Protocol is an
example of a software abstraction for root bridges that is defined in the PCI
Bus Support chapter of the _UEFI Specification_.

UEFI drivers that produce root bridge abstractions do not follow the UEFI
driver model. Instead, they initialize hardware and directly produce the
handles and protocols in the driver entry point. Root bridge drivers are
slightly different from service drivers in the following ways:

* Root bridge drivers always creates new handles.

* It installs a software abstraction for each root bridge, such as the PCI Root
  Bridge I/O Protocol

* It installs a Device Path Protocol for each root bridge that describes the
  programmatic path to the root bridge device.

A root bridge driver may register an `Unload()` service, and that service would
be required to uninstall the protocols that were installed in the driver's
entry point.

**********
**Caution:** The `Unload()` service for a root bridge driver may be a
dangerous operation because there is no way for the root bridge driver to know
if the protocols it installed are being used by other UEFI components. If the
root bridge driver is unloaded and other UEFI components are still using the
protocols that were produced by the unloaded driver, then the system is likely
to fail.
**********

The example, below shows an example of a root bridge driver that produces one
handle for a system with a single PCI root bridge. A Device Path Protocol with
an ACPI device path node and the PCI Root Bridge I/O Protocol are installed
onto a newly created handle. The ACPI device path node for the PCI root bridge
must match the description of the PCI root bridge in the ACPI table for the
platform.

In this example, the Device Path Protocol and PCI Root Bridge I/O Protocol are
declared as global variables. Additional private data may need to be required
to properly manage a PCI root bridge.

###### Example 106-Single PCI root bridge driver entry point

```c
#include <Uefi.h>
#include <Protocol/DevicePath.h>
#include <Protocol/PciRootBridgeIo.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/DevicePathLib.h>

//
// Structure defintion for the device path of a PCI Root Bridge
//
typedef struct {
  ACPI_HID_DEVICE_PATH AcpiDevicePath;
  EFI_DEVICE_PATH_PROTOCOL EndDevicePath;
} EFI_PCI_ROOT_BRIDGE_DEVICE_PATH;

//
// Handle for the PCI Root Bridge
//
EFI_HANDLE gAbcPciRootBridgeIoHandle = NULL;

//
// Device Path Protocol instance for the PCI Root Bridge
//
EFI_PCI_ROOT_BRIDGE_DEVICE_PATH gAbcPciRootBridgeIoDevicePath = {
 {
    ACPI_DEVICE_PATH,                              // Type
    ACPI_DP,                                       // Subtype
    (UINT8)(sizeof (ACPI_HID_DEVICE_PATH)),        // Length lower
    (UINT8)((sizeof (ACPI_HID_DEVICE_PATH)) >> 8), // Length upper
    EISA_PNP_ID (0x0A03),                          // HID
    0                                              // UID
  },
  {
    END_DEVICE_PATH_TYPE,                          // Type
    END_ENTIRE_DEVICE_PATH_SUBTYPE,                // Subtype
    END_DEVICE_PATH_LENGTH,                        // Length
    0                                              // Length
  }
};

//
// PCI Root Bridge I/O Protocol instance for the PCI Root Bridge
//
EFI_PCI_ROOT_BRIDGE_IO_PROTOCOL gAbcPciRootBridgeIo = {
  NULL,                                        // ParentHandle
  AbcPciRootBridgeIoPollMem,                   // PollMem()
  AbcPciRootBridgeIoPollIo,                    // PolIo()
  {
    AbcPciRootBridgeIoMemRead,                 // Mem.Read()
    AbcPciRootBridgeIoMemWrite                 // Mem.Write()
  },
  {
    AbcPciRootBridgeIoIoRead,                  // Io.Read()
    AbcPciRootBridgeIoIoWrite,                 // Io.Write()
  },
  {
    AbcPciRootBridgeIoPciRead,                 // Pci.Read()
    AbcPciRootBridgeIoPciWrite,                // Pci.Write()
  },
  AbcPciRootBridgeIoCopyMem,                   // CopyMem()
  AbcPciRootBridgeIoMap,                       // Map()
  AbcPciRootBridgeIoUnmap,                     // Unmap()
  AbcPciRootBridgeIoAllocateBuffer,            // AllocateBuffer()
  AbcPciRootBridgeIoFreeBuffer,                // FreeBuffer()
  AbcPciRootBridgeIoFlush,                     // Flush()
  AbcPciRootBridgeIoGetAttributes,             // GetAttributes()
  AbcPciRootBridgeIoSetAttributes,             // SetAttributes()
  AbcPciRootBridgeIoConfiguration,             // Configuration()
  0                                            // SegmentNumber
};

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE                                ImageHandle,
  IN EFI_SYSTEM_TABLE                          *SystemTable
  )
{
  //
  // Perform PCI Root Bridge initialization operations here
  //
  
  //
  // Install the Device Path Protocol and PCI Root Bridge I/O Protocol
  // onto a new handle.
  //
  return gBS->InstallMultipleProtocolInterfaces (
                &gAbcPciRootBridgeIoHandle,
                &gEfiDevicePathProtocolGuid,
                &gAbcPciRootBridgeIoDevicePath,
                &gEfiPciRootBridgeIoProtocolGuid,
                &gAbcPciRootBridgeIo,
                NULL
                );
}
```

The example below, shows an example for a root bridge driver that produces four
handles for a system with four PCI root bridges. A Device Path Protocol with an
ACPI device path node and the PCI Root Bridge I/O Protocol are installed onto
each of the newly created handles. The ACPI device path nodes for each of the
PCI root bridges must match the description of the PCI root bridges in the ACPI
tables for the platform.

In this example, the _UID field for the root bridges has the values of 0, 1, 2,
and 3 However, there is no requirement that the _UID field starts at 0 or that
they are contiguous. The only requirement is that the _UID field for each root
bridge matches the _UID field in the ACPI table describing the same root bridge
controller.

Templates for the Device Path Protocol and PCI Root Bridge I/O Protocol are
declared as global variables, and copies of those global variable template are
made for each PCI root bridge using the `AllocateCopyPool()` function in the
EDK II library `MemoryAllocationLib`. Additional private data may need to be
required to properly manage a group of PCI root bridges.

###### Example 107-Multiple PCI root bridge driver entry point

```c
#include <Uefi.h>
#include <Protocol/DevicePath.h>
#include <Protocol/PciRootBridgeIo.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/MemoryAllocationLib.h>
#include <Library/DevicePathLib.h>
#include <Library/DebugLib.h>

//
// Structure defintion for the device path of a PCI Root Bridge
//
typedef struct {
  ACPI_HID_DEVICE_PATH AcpiDevicePath;
  EFI_DEVICE_PATH_PROTOCOL EndDevicePath;
} EFI_PCI_ROOT_BRIDGE_DEVICE_PATH;

//
// Device Path Protocol instance for the PCI Root Bridge
//
EFI_PCI_ROOT_BRIDGE_DEVICE_PATH gAbcPciRootBridgeIoDevicePathTemplate = {
  {
    ACPI_DEVICE_PATH,                               // Type
    ACPI_DP,                                        // Subtype
    (UINT8)(sizeof (ACPI_HID_DEVICE_PATH)),         // Length lower
    (UINT8)((sizeof (ACPI_HID_DEVICE_PATH)) >> 8),  // Length upper
    EISA_PNP_ID (0x0A03),                           // HID
    0                                               // UID
  },
  {
    END_DEVICE_PATH_TYPE,                           // Type
    END_ENTIRE_DEVICE_PATH_SUBTYPE,                 // Subtype
    END_DEVICE_PATH_LENGTH,                         // Length
    0                                               // Length
  }
};

//
// PCI Root Bridge I/O Protocol instance for the PCI Root Bridge
//
EFI_PCI_ROOT_BRIDGE_IO_PROTOCOL gAbcPciRootBridgeIoTemplate = {
  NULL,                             // ParentHandle
  AbcPciRootBridgeIoPollMem,        // PollMem()
  AbcPciRootBridgeIoPollIo,         // PolIo() {
  AbcPciRootBridgeIoMemRead,        // Mem.Read()
  AbcPciRootBridgeIoMemWrite        // Mem.Write()
  },
  {
    AbcPciRootBridgeIoIoRead,       // Io.Read()
    AbcPciRootBridgeIoIoWrite,      // Io.Write()
  },
  {
  AbcPciRootBridgeIoPciRead,        // Pci.Read()
  AbcPciRootBridgeIoPciWrite,       // Pci.Write() },
  AbcPciRootBridgeIoCopyMem,        // CopyMem()
  AbcPciRootBridgeIoMap,            // Map()
  AbcPciRootBridgeIoUnmap,          // Unmap()
  AbcPciRootBridgeIoAllocateBuffer, // AllocateBuffer()
  AbcPciRootBridgeIoFreeBuffer,     // FreeBuffer()
  AbcPciRootBridgeIoFlush,          // Flush()
  AbcPciRootBridgeIoGetAttributes,  // GetAttributes()
  AbcPciRootBridgeIoSetAttributes,  // SetAttributes()
  AbcPciRootBridgeIoConfiguration,  // Configuration()
  0                                 // SegmentNumber
};

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  EFI_STATUS                       Status;
  UINTN                            Index;
  EFI_HANDLE                       NewHandle;
  EFI_PCI_ROOT_BRIDGE_DEVICE_PATH  *DevicePath;
  EFI_PCI_ROOT_BRIDGE_IO_PROTOCOL  *PciRootBridgeIo;
  
  //
  // Perform PCI Root Bridge initialization operations here
  //
  
  for (Index = 0;
       Index < 4; Index++) {
    //
    // Allocate and initialize Device Path Protocol
    //
    DevicePath = AllocateCopyPool (
                   sizeof (gAbcPciRootBridgeIoDevicePathTemplate),
                   &gAbcPciRootBridgeIoDevicePathTemplate
                   );
    ASSERT (DevicePath != NULL);
    DevicePath->AcpiDevicePath.UID = (UINT32)Index;
	
    //
    // Allocate and initialize PCI Root Bridge I/O Protocol
    //
    PciRootBridgeIo = AllocateCopyPool (
                        sizeof (gAbcPciRootBridgeIoTemplate),
                        &gAbcPciRootBridgeIoTemplate
                        );
    ASSERT (PciRootBridgeIo != NULL);
	
    //
    // Install the Device Path Protocol and PCI Root Bridge I/O Protocol
    // onto a new handle.
    //
    NewHandle = NULL;
    Status = gBS->InstallMultipleProtocolInterfaces (
                    &NewHandle,
                    &gEfiDevicePathProtocolGuid,
                    DevicePath,
                    &gEfiPciRootBridgeIoProtocolGuid,
                    PciRootBridgeIo,
                    NULL
                    );
    ASSERT_EFI_ERROR (Status);
  }
  return EFI_SUCCESS;
}
```
