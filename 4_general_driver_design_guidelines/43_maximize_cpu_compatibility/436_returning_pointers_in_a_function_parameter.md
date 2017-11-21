<!--- @file
  4.3.6 Returning Pointers in a Function Parameter

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

### 4.3.6 Returning Pointers in a Function Parameter

The following example shows a bad example for casting pointers. The function
`MyFunction()` returns a 64-bit value in an `OUT` parameter that is assigned
from a 32-bit input parameter. There is nothing wrong with `MyFunction()`. The
problem is when `MyFunction()` is called. Here, the address of `B`, a 32-bit
container, is cast to a pointer to a 64-bit container and passed to
`MyFunction()`. `MyFunction()` writes to 64 bits starting at `B`. This location
happens to overwrite the value of `B` and the value of `A` in the calling
function.

The first `Print()` correctly shows the values of `A` and `B`. The second
`Print()` shows that `B` was given `A`'s original value, but the contents of
`A` were destroyed and overwritten with a 0.

The cast from `&B` to a `(UINT64 *)` is the problem here. This code compiles
without errors or warnings on both 32-bit and 64-bit processors. It executes on
32-bit and 64-bit processors with these unexpected side effects. It might also
generate an alignment fault on IPF if `&B` is not 64-bit aligned. One possible
fix for this issue is to change B from a `UINT32` to a `UINT64`.

###### Example 12-Casting OUT function parameters

```c
EFI_STATUS 
EFIAPI
MyFunction (
  IN  UINT32  ValueU32,
  OUT UINT64  *ValueU64
  )
{
  *ValueU64 = (UINT64)ValueU32;
  return EFI_SUCCESS;
}
UINT32 A;
UINT32 B;

A = 0x11112222;
B = 0x33334444;

//
// Prints "A = 11112222 B = 33334444"
//
Print (L"A = %08x B = %08x\n", A, B);

MyFunction (A, (UINT64 *)(&B));

//
// Prints "A = 00000000 B = 11112222"
//
Print (L"A = %08x B = %08x\n", A, B);
```
