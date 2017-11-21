<!--- @file
  5.1.2 Miscellaneous Services

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

### 5.1.2 Miscellaneous Services

The `SetMem()` and `CopyMem()` UEFI Boot Services are used by UEFI drivers to
initialize the contents of a buffer or copy a buffer from one location to
another. The `SetMem()` service is most commonly used to fill the contents of a
buffer with zeros after it is allocated. The `CopyMem()` service handles
buffers of any alignment and also handles the rare case when the source and
destination buffer overlap. With overlapping buffers, the requirement is that
the destination buffer on exit from this service must match the contents of the
source buffer on entry to this service.

The code fragments in this section also show examples that use the EDK II
library class `BaseMemoryLib` as an alternative to using the UEFI Boot Services
directly. The advantage of using this library class is that the source code can
be implemented just once. The EDK II DSC file used to build a UEFI Driver can
specify mappings to different implementations of the `BaseMemoryLib` library
class that meet the requirements of a specific target. For example, the
`MdePkg/Library/UefiMemoryLib` library instance uses the recommended UEFI Boot
Services `SetMem()` and `CopyMem()`are for best performance when building a
UEFI Driver for EBC. For best performance on IA32 or X64, use the SSE2
optimized `MdePkg/Library/BaseMemoryLibSse2` library instance.

#### 5.1.2.1 Code examples for SetMem()

Use the `SetMem()` UEFI Boot Service to initialize the contents of a buffer
with a specified value. UEFI drivers most commonly use this service to zero an
allocated buffer, but it can also be used to fill a buffer with other values.
The following code fragment in the _example below_ shows the same example from
_Example 16_, but uses `SetMem()` UEFI Boot Service to zero the contents of the
allocated buffer. The EDK II library

`UefiBootServicesTableLib` provides global variables for the UEFI System Table,
the UEFI Boot Services Table, and the Image Handle for the currently executing
driver. Here, the global variable for the UEFI Boot Services Table called `gBS`
is used to call the UEFI Boot Services `AllocatePool()` and `SetMem().`

###### Example 23-Allocate and clear a buffer using UEFI Boot Services

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS Status;
EXAMPLE_DEVICE *Device;

//
// Allocate a buffer for a data structure
//
Status = gBS->AllocatePool (
                EfiBootServicesData,
                sizeof (EXAMPLE_DEVICE),
                (VOID **)&Device
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Zero the contents of the allocated buffer
//
gBS->SetMem (Device, sizeof (EXAMPLE_DEVICE), 0);
```

The following code fragment shows the same example from _Example 17_, but uses
the `SetMem()` function from the EDK II library class `BaseMemoryLib` to zero
the contents of the allocated buffer.

###### Example 24-Allocate and clear a buffer using BaseMemoryLib

```c
#include <Uefi.h>
#include <Library/MemoryAllocationLib.h>
#include <Library/BaseMemoryLib.h>

EXAMPLE_DEVICE *Device;

//
// Allocate a buffer for a data structure
//
Device = (EXAMPLE_DEVICE *)AllocatePool (sizeof (EXAMPLE_DEVICE));
if (Device == NULL) {
  return EFI_OUT_OF_RESOURCES;
}

//
// Zero the contents of the allocated buffer
//
SetMem (Device, sizeof (EXAMPLE_DEVICE), 0);
```

The code fragment in Example 25, below, shows the same example from _Example
17_, above, but uses the `ZeroMem()` function from the EDK II library class
`BaseMemoryLib` to zero the contents of the allocated buffer.

###### Example 25-Allocate and clear a buffer using BaseMemoryLib

```c
#include <Uefi.h>
#include <Library/MemoryAllocationLib.h>
#include <Library/BaseMemoryLib.h>

EXAMPLE_DEVICE *Device;

//
// Allocate a buffer for a data structure
//
Device = (EXAMPLE_DEVICE *)AllocatePool (sizeof (EXAMPLE_DEVICE));
if (Device == NULL) {
  return EFI_OUT_OF_RESOURCES;
}

//
// Zero the contents of the allocated buffer
//
ZeroMem (Device, sizeof (EXAMPLE_DEVICE));
```

#### 5.1.2.2 Code examples for CopyMem()

The following code fragment shows an example of how the `CopyMem()` UEFI Boot
Service is typically used to copy an existing buffer into a newly allocated
buffer. The `AllocatePool()` function from the EDK II library
`MemoryAllocationLib` is used to allocate a new buffer. The EDK II library
`UefiBootServicesTableLib` provides global variables for the UEFI System Table,
the UEFI Boot Services Table, and the Image Handle for the currently executing
driver. In this example, the global variable for the UEFI Boot Services Table
called `gBS` is used to call the UEFI Boot Service `CopyMem()`.

###### Example 26-Allocate and copy buffer

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/MemoryAllocationLib.h>

EXAMPLE_DEVICE *SourceDevice;
EXAMPLE_DEVICE *Device;

//
// Allocate a buffer for a data structure
//
Device = (EXAMPLE_DEVICE *)AllocatePool (sizeof (EXAMPLE_DEVICE));
if (Device == NULL) {
  return EFI_OUT_OF_RESOURCES;
}

//
// Copy contents of SourceDevice to the allocated Device
//
gBS->CopyMem (Device, SourceDevice, sizeof (EXAMPLE_DEVICE));
```

The code fragment in Example 27, below, shows the same example from Example 26,
above, but uses the `CopyMem()` function from the EDK II library class
`BaseMemoryLib` to copy the contents of an existing buffer to a newly allocated
buffer.

###### Example 27-Allocate and clear a buffer using BaseMemoryLib

```c
#include <Uefi.h>
#include <Library/MemoryAllocationLib.h>
#include <Library/BaseMemoryLib.h>

EXAMPLE_DEVICE *SourceDevice;
EXAMPLE_DEVICE *Device;

//
// Allocate a buffer for a data structure
//
Device = (EXAMPLE_DEVICE *)AllocatePool (sizeof (EXAMPLE_DEVICE));
if (Device == NULL) {
  return EFI_OUT_OF_RESOURCES;
}

//
// Copy contents of SourceDevice to the allocated Device
//
CopyMem (Device, SourceDevice, sizeof (EXAMPLE_DEVICE));
```
