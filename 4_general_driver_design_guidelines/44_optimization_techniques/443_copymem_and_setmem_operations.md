<!--- @file
  4.4.3 CopyMem() and SetMem() Operations

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

### 4.4.3 CopyMem() and SetMem() Operations

The following example shows how to use the EDK II library `BaseMemoryLib`
functions `CopyMem()` and `SetMem()`to improve the performance of a UEFI
driver. These techniques apply to arrays, structures, or allocated buffers.

**Note:** By default, the EDK II enables high levels of optimization, so this
example may not build for all compilers because the loops are optimized into
intrinsics that can cause the link to fail. So not only does use of
'CopyMem()' and 'SetMem()' improve performance, it also improves UEFI
Driver portability.

###### Example 15-CopyMem() and SetMem() Speed Optimizations

```c
#include <Uefi.h>

typedef struct {
  UINT8 First;
  UINT32 Second;
} MY_STRUCTURE;

UINTN Index;
UINT8 A[100];
UINT8 B[100];
MY_STRUCTURE MyStructureA;
MY_STRUCTURE MyStructureB;

//
// Using a loop is slow or structure assignments is slow
//
for (Index = 0; Index < 100; Index++) {
  A[Index] = B[Index];
}
MyStructureA = MyStructureB;

//
// Using the optimized CopyMem() Boot Services is fast
//
CopyMem (
  (VOID *)A,
  (VOID *)B,
  100
  );
CopyMem (
  (VOID *)&MyStructureA,
  (VOID *)&MyStructureB,
  sizeof (MY_STRUCTURE)
  );
  
//
// Using a loop or individual assignment statements is slow
//
for (Index = 0; Index < 100; Index++) {
  A[Index] = 0;
}
MyStructureA.First = 0;
MyStructureA.Second = 0;

//
// Using the optimized SetMem() Boot Service is fast.
//
SetMem ((VOID *)A, 100, 0);
SetMem ((VOID *)&MyStructureA, sizeof (MY_STRUCTURE), 0);
```
