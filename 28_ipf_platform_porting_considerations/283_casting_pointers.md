<!--- @file
  28.3 Casting Pointers

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

## 28.3 Casting Pointers

The example below shows an example that generates an alignment fault on an IPF
platform. The first read access through _SmallValuePointer_ is aligned because
_LargeValue_ is on a 64-bit boundary. However, the second read access though
_SmallValuePointer_ generates an alignment fault because _SmallValuePointer_ is
not on a 32-bit boundary. The problem is that an 8-bit pointer was cast to a
32-bit pointer. Whenever a cast is made from a pointer to a smaller data type
to a pointer to a larger data type, there is a chance that the pointer to the
larger data type is unaligned.

###### Example 243-Pointer-cast alignment fault

```c
#include <Uefi.h>

UINT64 LargeValue;
UINT32 *SmallValuePointer;
UINT32 SmallValue;

SmallValuePointer = (UINT32 *) &LargeValue;

//
// Works
//
SmallValue        = *SmallValuePointer;
SmallValuePointer = (UINT32 *)((UINT8 *)&LargeValue + 1);
//
// Fails. Generates an alignment fault
//
SmallValue        = *SmallValuePointer;
```

Example 244, below, shows the same example as Example 243, above, but has been
modified to prevent the alignment fault. The second read access through
_SmallValuePointer_ is replaced with a call to the EDK II library `BaseLib`
function called `ReadUnaligned32()` that treats the 32-bit value as an array of
bytes. The individual bytes are read and combined into a 32-bit value. The
generated object code is larger and slower, but it is functional on all CPU
architectures supported by the _UEFI Specification_.

###### Example 244-Corrected pointer-cast alignment fault

```c
#include <Uefi.h>
#include <Library/BaseLib.h>

UINT64 LargeValue;
UINT32 *SmallValuePointer;
UINT32 SmallValue;

SmallValuePointer = (UINT32 *) &LargeValue;

//
// Works
//
SmallValue        = *SmallValuePointer;

SmallValuePointer = (UINT32 *)((UINT8 *)&LargeValue + 1);

//
// Works
//
SmallValue        = ReadUnaligned32 (SmallValuePointer);
```

EDK II library `BaseLib` provides several functions to help perform unaligned
accessed in a safe manner. These functions perform a direct access on CPU
architectures that do not generate alignment faults, and break the access up
into small aligned pieces on CPU architectures that do generate alignment
faults. The list of unaligned access functions from the EDK II library
`BaseLib` includes the following:

* `ReadUnaligned64()`

* `ReadUnaligned32()`

* `ReadUnaligned24()`

* `ReadUnaligned16()`

* `WriteUnaligned64()`

* `WriteUnaligned32()`

* `WriteUnaligned24()`

* `WriteUnaligned16()`
