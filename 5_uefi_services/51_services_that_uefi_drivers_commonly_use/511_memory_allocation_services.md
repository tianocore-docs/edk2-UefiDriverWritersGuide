<!--- @file
  5.1.1 Memory Allocation Services

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

### 5.1.1 Memory Allocation Services

The `AllocatePool()` and `FreePool()` boot services are used by UEFI drivers to
allocate and free small buffers that are guaranteed to be aligned on an 8-byte
boundary. These services are ideal for allocating and freeing data structures.

The `AllocatePages()` and `FreePages()` boot services are used by UEFI drivers
to allocate and free larger buffers that are guaranteed to be aligned on a 4 KB
boundary. These services allow buffers to be allocated at any available
address, at specific addresses, or below a specific address.

#### 5.1.1.1 Critical considerations for allocating memory

UEFI drivers should not make assumptions about the organization of system
memory. Because of this, allocating from specific addresses or below specific
addresses is strongly discouraged. The `AllocatePool()` service _does not_
allow the caller to specify a preferred address, so this service is safe to use
and does not impact the compatibility of a UEFI Driver on different platforms.

The `AllocatePages()` service _does_ have a mode that allows a specific address
to be specified or a range of addresses to be specified. The allocation type of

`AllocateAnyPages` is safe to use and increases the compatibility of UEFI
Drivers on different platforms. The allocation types of `AllocateMaxAddress`
and `AllocateAddress` may reduce platform compatibility, so their use is
discouraged.

**********
**Caution:** Although the Allocate services allow for specific memory
allocation, **do not** allocate specific addresses in a UEFI driver. Allocating
buffers at a specific address could result in errors, including a catastrophic
failure on some platforms. Memory allocation in UEFI drivers should be done
dynamically.
**********
**TIP:** Always check function return codes to verify if a memory allocation
request succeeded or not before accessing the allocated buffer.
**********

Key points:

* To prevent memory leaks, every allocation operation must have a corresponding
  free operation. It is important to note that some UEFI services allocate
  buffers for the caller and expect the caller to free those buffers.

* Buffers above 4 GB may be allocated if there is system memory is present
  above 4 GB. As a result, all UEFI drivers must be aware that pointers may
  contain address values above 4 GB, and care must be taken never to strip the
  upper address bits.

* Structures and values placed in allocated buffers must be naturally aligned
  to maximize compatibility with all CPU architectures.

* Never use an allocated buffer for DMA without mapping it through an I/O
  Protocol. For example, the `Map()` and `UnMap()` services in the PCI I/O
  Protocol.

Refer to _Chapter 4_ for general porting considerations covering more memory
allocation details for 32-bit and 64-bit architectures.

#### 5.1.1.2 Do not directly allocate a memory buffer for DMA access

A UEFI driver must _never_ directly allocate a memory buffer for DMA access.
The UEFI driver cannot know enough about the system architecture to predict
what system memory areas are available for DMA or if CPU caches are coherent
with DMA operations. Instead, a UEFI driver must use the services provided by
the I/O protocol for the bus to allocate and free buffers required for DMA
operations. There should also be services to initiate and complete DMA
transactions. For example, the PCI Root Bridge I/O Protocol and PCI I/O
Protocol both provide services for PCI DMA operations. As additional I/O bus
types with DMA capabilities are introduced, new protocols that abstract the DMA
services must be provided.

#### 5.1.1.3 Allocating and freeing buffers

UEFI boot service drivers typically allocate and free buffers of type
`EfiBootServicesData`. UEFI runtime drivers typically allocate and free buffers
of type `EfiRuntimeServicesData`. OS Loaders typically allocate and free
buffers of type `EfiLoaderData`.

Most drivers that follow the UEFI driver model allocate private context
structures in their Driver Binding Protocol `Start()` function and free them in
their Driver Binding Protocol `Stop()` function. UEFI drivers may also
dynamically allocate and free buffers as different I/O operations are performed.

#### 5.1.1.4 Code examples for AllocatePool() and FreePool()

The following code fragment shows how the UEFI Boot Service `AllocatePool()` and
`FreePool()` can be used to allocate and free a buffer for a data structure
from `EfiBootServicesData` memory. The EDK II library
`UefiBootServicesTableLib` provides global variables for the UEFI System Table,
the UEFI Boot Services Table, and the Image Handle for the currently executing
driver. In this example, the global variable for the UEFI Boot Services Table,
called `gBS,` is used to call the UEFI Boot Services `AllocatePool()` and
`FreePool().`

###### Example 16-Allocate and free pool using UEFI Boot Services Table

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
// Free the allocated buffer
//
Status = gBS->FreePool (Device);
if (EFI_ERROR (Status)) {
  return Status;
}
```

The _code fragment_ below shows exactly the same functionality as Example 16,
above, but uses EDK II library `MemoryAllocationLib` to simplify the
implementation. The `MemoryAllocationLib` function `AllocatePool()` allocates memory of type
`EfiBootServicesData`. If memory of type `EfiRuntimeServicesData` is required,
then the `MemoryAllocationLib` function `AllocateRuntimePool()` should be used.

###### Example 17-Allocate and free pool using MemoryAllocationLib

```c
#include <Uefi.h>
#include <Library/MemoryAllocationLib.h>

EXAMPLE_DEVICE *Device;

//
// Allocate a buffer for a data structure
//
Device = (EXAMPLE_DEVICE *)AllocatePool (sizeof (EXAMPLE_DEVICE));
if (Device == NULL) {
  return EFI_OUT_OF_RESOURCES;
}

//
// Free the allocated buffer
//
FreePool (Device);
```

In many cases, when a structure is allocated, it is useful to clear the buffer
to a known state with zeros. The following code fragment in Example 18 expands
on Example 17, above, showing how the EDK II library `MemoryAllocationLib` can
be used to allocate and clear a buffer in a single call.

###### Example 18-Allocate and clear pool using MemoryAllocationLib

```c
#include <Uefi.h>
#include <Library/MemoryAllocationLib.h>

EXAMPLE_DEVICE *Device;
//
// Allocate and clear a buffer for a data structure
//
Device = (EXAMPLE_DEVICE *)AllocateZeroPool (sizeof (EXAMPLE_DEVICE));
if (Device == NULL) {
  return EFI_OUT_OF_RESOURCES;
}
//
// Free the allocated buffer
//
FreePool (Device);
```

Complex structures that require many fields to be initialized after the
structure is allocated may increase the size of the UEFI driver if the fields
are initialized one by one. The EDK II library `MemoryAllocationLib` provides
an additional allocation method that makes use of a template structure to
reduce code size.

The concept is that a template structure can be declared as a global variable
with all the fields pre-initialized to the required values. It takes less space
to store just the data than it does to store the instructions and data to
initialize all the fields one by one. This technique may be useful for UEFI
Drivers that produce new protocols for each device the UEFI Driver manages.
_Example 19_, below, expands on the above Example 18 showing how the EDK II library
`MemoryAllocationLib` is used to allocate and initialize a buffer from a template
structure in a single call.

###### Example 19-Allocate and initialize pool using MemoryAllocationLib

```c
#include <Uefi.h>
#include <Library/MemoryAllocationLib.h>

EXAMPLE_DEVICE gExampleDeviceTemplate = {
  EXAMPLE_PRIVATE_DATA_SIGNATURE,
  //
  // Other device specific fields
  //
};

EXAMPLE_DEVICE *Device;

//
// Allocate and initialize a buffer for a data structure
//
Device = (EXAMPLE_DEVICE *)AllocateCopyPool (
                             sizeof (EXAMPLE_DEVICE), &gExampleDeviceTemplate
                             );
if (Device == NULL) {
  return EFI_OUT_OF_RESOURCES;
}

//
// Free the allocated buffer
//
FreePool (Device);
```

#### 5.1.1.5 Code examples for AllocatePages() and FreePages()

The following code fragment shows how the UEFI Boot Services `AllocatePages()`
and `FreePages()` are used to allocate and free a 16KB buffer for a data structure
from `EfiBootServicesData` memory. The EDK II library `UefiBootServicesTableLib`
provides global variables for the UEFI System Table, the UEFI Boot Services Table,
and the Image Handle for the currently executing driver. In this example, the global
variable for the UEFI Boot Services Table, called `gBS`, is used to call the
UEFI Boot Services `AllocatePages()` and `FreePages().`

###### Example 20-Allocate and free pages using UEFI Boot Services Table

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS Status;
EFI_PHYSICAL_ADDRESS PhysicalBuffer;
UINTN Pages;
VOID *Buffer;

//
// Allocate the number of pages to hold Size bytes and
// return in PhysicalBuffer
//
Pages = EFI_SIZE_TO_PAGES (SIZE_16KB);
Status = gBS->AllocatePages (
                AllocateAnyPages,
                EfiBootServicesData,
                Pages,
                &PhysicalBuffer
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Convert the physical address to a pointer.
// This method works for all support CPU architectures.
//
Buffer = (VOID *)(UINTN)PhysicalBuffer;

//
// Free the allocated buffer
//
Status = gBS->FreePages (PhysicalBuffer, Pages);
if (EFI_ERROR (Status)) {
  return Status;
}
```

The code fragment in Example 21, below, shows the same functionality as Example
20, above, but uses the EDK II library `MemoryAllocationLib` to simplify the
implementation.

The `MemoryAllocationLib` function `AllocatePages()` allocates memory of type
`EfiBootServicesData`. If memory of type `EfiRuntimeServicesData` is required,
the `MemoryAllocationLib` function `AllocateRuntimePages()` should be used.

###### Example 21-Allocate and free pages using MemoryAllocationLib

```c
#include <Uefi.h>
#include <Library/MemoryAllocationLib.h>

EXAMPLE_DEVICE *Device;
UINTN Pages;

//
// Allocate a buffer for a data structure
//
Pages = EFI_SIZE_TO_PAGES (sizeof (EXAMPLE_DEVICE));
Device = (EXAMPLE_DEVICE *)AllocatePages (Pages);
if (Device == NULL) {
  return EFI_OUT_OF_RESOURCES;
}

//
// Free the allocated buffer
//
FreePages (Device, Pages);
```

In some rare circumstances, a UEFI Driver may be required to allocate a buffer
with a specific alignment. `AllocatePool()` provides 8-byte alignment.
`AllocatePages()` provides 4KB alignment. If an alignment above 4KB is
required, the preferred technique is to allocate a large buffer through
`AllocatePages()`, find the portion of the allocated buffer that meets the
required alignment, and free the unused portions. EDK II library
`MemoryAllocationLib` provides the function called `AllocateAlignedPages()`
that implements this technique. The code fragment in the example below
allocates a 16KB buffer aligned on a 64KB boundary.

<div style="page-break-after: always;"></div>

###### Example 22-Allocate and free aligned pages using MemoryAllocationLib

```c
#include <Uefi.h>
#include <Library/MemoryAllocationLib.h>

VOID *Buffer;
UINTN Pages;

//
// Allocate a buffer for a data structure
//
Pages = EFI_SIZE_TO_PAGES (SIZE_16KB);
Buffer = (EXAMPLE_DEVICE *)AllocateAlignedPages (Pages, SIZE_64KB);
if (Buffer == NULL) {
  return EFI_OUT_OF_RESOURCES;
}

//
// Free the allocated buffer
//
FreePages (Buffer, Pages);
```
