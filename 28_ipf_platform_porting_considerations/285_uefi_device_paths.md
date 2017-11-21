<!--- @file
  28.5 UEFI Device Paths

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

## 28.5 UEFI Device Paths

The technique of using the EDK II library BaseLib functions to perform
unaligned reads and writes is functional, but can become tedious if a large
number of fields in data structures need to be accessed. In these cases, it may
be necessary to copy a data structure from an unaligned source location to an
aligned destination location so that the fields of the data structure can be
accessed without generating an alignment fault. Two examples of this scenario
are parsing UEFI device path nodes and parsing network packets.

The device path nodes in a UEFI device path are packed together so they take up
as little space as possible when they are stored in environment variables such
as _ConIn_, _ConOut_, _StdErr_, _Boot####_, and _Driver####_. As a result,
individual device path nodes may not be aligned on a 64-bit boundary. UEFI
device paths and UEFI device paths nodes may be passed around as opaque data
structures, but whenever the fields of a UEFI device path node are accessed,
the device path node must be copied to a location that is guaranteed to be on a
64-bit boundary. Likewise, network packets are packed so they take up as little
space as possible. As each layer of a network packet is examined, the packet
may need to be copied to a 64-bit aligned location before the individual fields
of the packet are examined.

The following example shows an example of a function that parses a UEFI device
path and extracts the 32-bit HID and UID from an ACPI device path node. This
example generates an alignment fault if _DevicePath_ is not aligned on a 32-bit
boundary.

###### Example 247-UEFI device path node alignment fault

```c
#include <Uefi.h>
#include <Protocol/DevicePath.h>

VOID
EFIAPI
GetAcpiHidUid (
   EFI_DEVICE_PATH_PROTOCOL  *DevicePath,
   UINT32                    *Hid,
   UINT32                    *Uid
  )
{
  ACPI_HID_DEVICE_PATH  *AcpiDevicePath;
  
  AcpiDevicePath = (ACPI_HID_DEVICE_PATH *)DevicePath;
  
  //
  // Wrong. May cause an alignment fault.
  //
  *Hid = AcpiDevicePath->HID;
  
  //
  // Wrong. May cause an alignment fault.
  //
  *Uid = AcpiDevicePath->UID;
}
```

Example 248, below, shows the corrected version of Example 247, above. Because
the alignment of _DevicePath_ cannot be guaranteed, the solution is to copy the
ACPI device path node from _DevicePath_ into an ACPI device path node structure
that is declared as the local variable _AcpiDevicePath_. A structure declared
as a local variable is guaranteed to be on a 64-bit boundary on IPF platforms.
The fields of the ACPI device path node can then be safely accessed without
generating an alignment fault.

###### Example 248-Corrected UEFI device path node alignment fault

```c
#include <Uefi.h>
#include <Protocol/DevicePath.h>
#include <Library/BaseMemoryLib.h>

VOID
EFIAPI
GetAcpiHidUid (
   EFI_DEVICE_PATH_PROTOCOL  *DevicePath,
   UINT32                    *Hid,
   UINT32                    *Uid
  )
{
  ACPI_HID_DEVICE_PATH  AcpiDevicePath;
  
  CopyMem (&AcpiDevicePath, DevicePath, sizeof (ACPI_HID_DEVICE_PATH));
  
  //
  // Correct. Guaranteed not to generate an alignment fault.
  //
  *Hid = AcpiDevicePath.HID;
  
  //
  // Correct. Guaranteed not to generate an alignment fault.
  //
  *Uid = AcpiDevicePath.UID;
}
```
