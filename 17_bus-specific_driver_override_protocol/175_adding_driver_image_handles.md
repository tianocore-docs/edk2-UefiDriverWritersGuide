<!--- @file
  17.5 Adding Driver Image Handles

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

## 17.5 Adding Driver Image Handles

The example below shows an internal worker function that adds a driver image
handle to the ordered list of driver image handles in the private context data
structure. This function is used by the bus driver to register image handles
associated with UEFI Drivers discovered on child devices (i.e. when the PCI bus
driver discovered UEFI Drivers stored in PCI option ROMs). As each UEFI driver
is loaded, this internal worker function is called to add the image handle of
the UEFI driver to the Bus Specific Driver Override Protocol. The order that
the image handles are registered with `AbcAddDriver()` is the order in which
they are returned from `GetDriver()`.

If there is not enough room in the image handle array, an array with 10
additional handles is allocated. The contents of the old array are transferred
to the new array and the old array is freed. The EDK II library
`MemoryAllocationLib` provides the `ReallocatePool()` function, simplifying the implementations of UEFI Drivers
required to manage dynamic memory. Lacking enough memory to allocate the new
array, the `EFI_OUT_OF_RESOURCES` is returned. Once there is enough room to
store the new image handle, the image handle is added to the end of the array
and `EFI_SUCCESS` is returned.

###### Example 168-Adding Driver Image Handles

```c
#include <Uefi.h>
#include <Librray/MemoryAllocationLib.h>

EFI_STATUS EFIAPI
AbcAddDriver (
  IN ABC_PRIVATE_DATA  *Private,
  IN EFI_HANDLE        DriverImageHandle
  )
{
  EFI_HANDLE           *NewBuffer;
  
  if (Private->NumberOfHandles >= Private->HandleBufferSize) {
    NewBuffer = ReallocatePool (
                  Private->HandleBufferSize * sizeof (EFI_HANDLE),
                  (Private->HandleBufferSize + 10) * sizeof (EFI_HANDLE),
                  Private->HandleBuffer
                  );
    if (NewBuffer == NULL) {
      return EFI_OUT_OF_RESOURCES;
    }
    Private->HandleBufferSize += 10;
    Private->HandleBuffer = NewBuffer;
  }
  Private->HandleBuffer[Private->NumberOfHandles] = DriverImageHandle;
  Private->NumberOfHandles++;
  return EFI_SUCCESS;
}
```
