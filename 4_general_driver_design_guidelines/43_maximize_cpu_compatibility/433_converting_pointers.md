<!--- @file
  4.3.3 Converting pointers

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

### 4.3.3 Converting pointers

Be mindful when converting physical addresses to pointers on 64-bit
architectures. All UEFI driver writers must be aware that pointers may contain
values above 4 GB, and that care must be taken never to strip the upper address
bits. If the upper address bits are stripped, the driver _may_ work on 32-bit
architectures, and on 64-bit architectures with small memory configurations,
but _may not_ work on 64-bit platforms with larger memory configurations.

**********
**Note:** _Make sure the driver does not strip the upper address bits when
converting pointers._
**********

#### 4.3.3.1 The Exception to the Rule

There is one exception to this rule of casting pointers and it applies to
both32-bit and 64-bit processors. The data types `INTN` and `UINTN` are the
exact same size of pointers on both 32-bit and 64-bit platforms, which means
that a pointer can be cast to or from `INTN` or `UINTN` without any adverse
side effects. However, ANSI C does not require function pointers to be the same
size as data pointers. Also, function pointers and data pointers are not
required to be the same size as `INTN` or `UINTN`. As a result, this exception
does not apply to all processors.

#### 4.3.3.2 Identifying a Pointer Problem

Problems caused by mistakes in pointer casting are difficult to catch. This is
so because explicit casts are required to cast a fixed-width type to a pointer
or vice versa. Once these explicit type casts are introduced, no compiler
warnings or errors are generated. In fact, the code may execute fine on, for
example, 32-bit platforms and on 64-bit platforms with physical memory below 4
GB. The only failing case is when the code is tested on a 64-bit system with
physical memory above 4 GB. The symptom is typically a processor exception that
results in a system hang or reset.

The example below shows some good and bad examples of casting pointers. The
first group is casting pointers to pointers. The second group is casting
pointers to fixed width types, and the last group is casting fixed width types
to pointers.

###### Example 10-Examples of casting pointers

```c
#include <Uefi.h>

typedef struct {
  UINT8 First;
  UINT32 Second;
} MY_STRUCTURE;

MY_STRUCTURE *MyStructure;
UINT8 ValueU8;
UINT16 ValueU16;
UINT32 ValueU32;
UINT64 ValueU64;
UINTN ValueUN;
INT64 Value64;
INTN ValueN;
VOID *Pointer;

//
// Casting pointers to pointers
//
Pointer = (VOID *)MyStructure;           // Good.
MyStructure = (MY_STRUCTURE *)Pointer;   // Good.

//
// Casting pointers to fixed width types
//
ValueU8 = (UINT8)MyStructure;            // Bad. Strips upper 24 bits on 32-bit CPU.
                                         // Strips upper 56 bits on 64-bit CPU.
ValueU16 = (UINT16)MyStructure;          // Bad. Strips upper 16 bits on 32-bit CPU.
                                         // Strips upper 48 bits on 64-bit CPU. ValueU32 = (UINT32)MyStructure;
                                         // Bad. Works on 32-bit CPUs.
                                         // Strips upper 32 bits on 64-bit CPU.
ValueU64 = (UINT64)MyStructure;          // Good. Works on all architectures
Value64 = (INT64)MyStructure;            // Good. Works on all architectures
ValueUN = (UINTN)MyStructure;            // Good. Works on all architectures
ValueN = (INTN)MyStructure;              // Good. Works on all architectures

//
// Casting fixed width types to pointers
//
MyStructure = (MY_STRUCTURE *)ValueU8;   // Bad.
MyStructure = (MY_STRUCTURE *)ValueU16;  // Bad.
MyStructure = (MY_STRUCTURE *)ValueU32;  // Bad. Works on 32-bit CPUs.
                                         // Works on 64-bit CPU with < 4GB memory
                                         // Strips upper 32 bits on 64-bit CPU

MyStructure = (MY_STRUCTURE *)ValueU64;  // Good. Works on all architectures
MyStructure = (MY_STRUCTURE *)Value64;   // Good. Works on all architectures
MyStructure = (MY_STRUCTURE *)ValueUN;   // Good. Works on all architectures 
MyStructure = (MY_STRUCTURE *)ValueN;    // Good. Works on all architectures
```
