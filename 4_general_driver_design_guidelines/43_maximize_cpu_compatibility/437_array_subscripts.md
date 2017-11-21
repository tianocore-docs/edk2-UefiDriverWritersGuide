<!--- @file
  4.3.7 Array Subscripts

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

### 4.3.7 Array Subscripts

In general, array subscripts should be of type `INTN` or `UINTN`. Using these
types avoids problems if an array subscript is decremented below `0`. If a
`UINT32` is used as an array subscript and is decremented below 0, it is
decremented to `0xFFFFFFFF` on a 32-bit processor and `0x00000000FFFFFFFF` on a
64-bit processor. These subscript values are very different. On 32-bit
architectures, this value is the same indexing element as `-1` of the array.
However, on a 64-bit processor, this value is the same indexing element as
`0xFFFFFFFF` of the array.

If an `INTN` or `UINTN` is used instead of a `UINT32` for the array subscript,
then this problem goes away. When a `UINTN` is decremented below `0`, it is
decremented to `0xFFFFFFFF` on a 32-bit processor and `0xFFFFFFFFFFFFFFFF` on a
64-bit processor. These values are both the same indexing element as `-1` of
the array.

The following example shows two array subscripts. The first one works on 32-bit
architectures but accesses a very high address on 64-bit architectures that may
generate a fault or hang condition. The second array subscript is rewritten to
work properly on both 32-bit architectures and 64-bit architectures.

###### Example 13-Array subscripts example

```c
UINT32 Index;
CHAR8 Array[] = "ABCDEFGHIJKLIMNOPQRSTUVWXYZ";
CHAR8 *MyArray;

MyArray = &(Array[5]);
Index = 0;

//
// Works on 32-bit CPUs
// Accesses high memory on 64-bit CPUs and may generate fault or hang
//
Print (L"Character = %c\n", Array[Index - 1]);

////////////////////////////////////////////////////////////////////////

UINTN Index;
CHAR8 Array[] = "ABCDEFGHIJKLIMNOPQRSTUVWXYZ";
CHAR8 *MyArray;

MyArray = &(Array[5]);
Index = 0;

//
// Works on 32-bit CPUs and 64-bit CPUs
//
Print (L"Character = %c\n", Array[Index - 1]);
```
