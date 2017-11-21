<!--- @file
  29.4 Use of sizeof()

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

## 29.4 Use of sizeof()

In some cases, `sizeof()` is computed at runtime for EBC code, whereas
`sizeof()` is never computed at runtime for native code. Because pointers and
the UEFI data types `INTN` and `UINTN` are different sizes on different CPU
architectures, an EBC image must adapt to the platform on which it is
executing. The example below shows several examples of simple and complex data
types. For the types that return different sizes for 32-bit versus 64-bit
processors, the EBC compiler generates code that computes the correct values at
runtime when executing on 32-bit and 64-bit processors.

###### Example 250-Size of data types with EBC

```c
#include <Uefi.h>

typedef enum {
  Red,
  Green,
  Blue
} COLOR_TYPE;

#pragma pack(1)
typedef struct {
  UINT64 ValueU64;
  UINT32 ValueU32;
  UINT16 ValueU16;
  UINT8 ValueU8;
} FIXED_STRUCTURE;

typedef struct {
  UINTN ValueUN;
  VOID *Pointer;
  UINT64 ValueU64;
  UINT32 ValueU32;
} VARIABLE_STRUCTURE;
#pragma pack()

UINT64 Size;

Size = sizeof (UINT64);        // 8 bytes on all CPUs
Print (L"Size = %d\n", Size);
Size = sizeof (UINT32);        // 4 bytes on all CPUs
Print (L"Size = %d\n", Size);
Size = sizeof (UINT16);        // 2 bytes on all CPUs
Print (L"Size = %d\n", Size);
Size = sizeof (UINT8);         // 1 byte on all CPUs
Print (L"Size = %d\n", Size);
Size = sizeof (UINTN);         // 4 bytes on 32-bit CPU, 8 bytes on 64-bit CPU
Print (L"Size = %d\n", Size);
Size = sizeof (INTN);          // 4 bytes on 32-bit CPU, 8 bytes on 64-bit CPU
Print (L"Size = %d\n", Size);
Size = sizeof (COLOR_TYPE);    // 4 bytes on 32-bit CPU, 8 bytes on 64-bit CPU
Print (L"Size = %d\n", Size);
Size = sizeof (VOID *);        // 4 bytes on 32-bit CPU, 8 bytes on 64-bit CPU
Print (L"Size = %d\n", Size);

//
// 15 bytes on 32-bit CPU, 15 bytes on 64-bit CPU
//
Size = sizeof (FIXED_STRUCTURE);
Print (L"Size = %d\n", Size);

//
// 20 bytes on 32-bit CPU, 28 bytes on 64-bit CPU
//
Size = sizeof (VARIABLE_STRUCTURE);
Print (L"Size = %d\n", Size);
```
