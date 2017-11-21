<!--- @file
  29.4.2 CASE Statements

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

### 29.4.2 CASE Statements

Because pointers and the data types `INTN` and `UINTN` are different sizes on
different instruction set architectures and case statements are determined at
compile time; the `sizeof()` function cannot be used in a `case` statement with
an indeterminately sized data type because the `sizeof()` function cannot be
evaluated to a constant by the EBC compiler at compile time. UEFI status codes
values such as `EFI_SUCCESS` and `EFI_UNSUPPORTED` are defined differently on different CPU architectures. As a result, UEFI status codes cannot be used in `case` expressions. The following
example shows examples using `case` statements.

###### Example 253-Case statements that fail for EBC

```c
#include <Uefi.h>

UINTN Value;

switch (Value) {
case 0:                    // Works because 0 is a constant.
  break;
case sizeof (UINT16):      // Works because sizeof (UINT16) is always 2 
  break; 
case sizeof (UINTN):       // EBC compiler error. sizeof (UINTN) is not constant.
  break;
case EFI_UNSUPPORTED:      // EBC compiler error. EFI_UNSUPPORTED is not constant.
  break;
}
```

One solution to this issue is to convert `case` statements into `if`/`else`
statements. The example below shows the equivalent functionality as Example
253, above, but does not generate any EBC compiler errors.

###### Example 254-Case statements that work for EBC

```c
#include <Uefi.h>

UINTN Value;

switch (Value) {
case 0:                  // Works because 0 is a constant.
  break;
case sizeof (UINT16):    // Works because sizeof (UINT16) is always 2
  break;
}
if (Value == sizeof (UINTN)) {
} else if (Value == EFI_UNSUPPORTED) {
}
```
