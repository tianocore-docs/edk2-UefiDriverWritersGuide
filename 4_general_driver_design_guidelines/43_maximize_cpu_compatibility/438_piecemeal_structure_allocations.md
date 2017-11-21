<!--- @file
  4.3.8 Piecemeal Structure Allocations

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

### 4.3.8 Piecemeal Structure Allocations

Structures should always be allocated using the `sizeof()` operator on the name
of the structure. Never use the sum of the sizes of the structure's members
because it does not take into account the padding that the compiler introduces
to guarantee alignment. The following example shows two examples for allocating
memory for a structure. The first allocation is incorrect, the second
allocation is correct.

###### Example 14-Incorrect and correct piecemeal structure allocation

```c
typedef struct {
  UINT8 Value8;
  UINT64 Value64;
} EXAMPLE_STRUCTURE;

EXAMPLE_STRUCTURE *Example;

//
// Wrong. This only allocates 9 bytes, but MyStructure is 16 bytes
//
Example = AllocatePool (sizeof (UINT8) + sizeof (UINT64));

//
// Correct. This allocates 16 bytes for MyStructure.
//
Example = AllocatePool (sizeof (EXAMPLE_STRUCTURE));
```
