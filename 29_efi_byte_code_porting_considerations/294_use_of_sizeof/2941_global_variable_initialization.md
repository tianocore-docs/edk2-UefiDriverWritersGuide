<!--- @file
  29.4.1 Global Variable Initialization

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

### 29.4.1 Global Variable Initialization

In a native compile the value of `sizeof (UINTN)` is computed by the compiler
at compile time. This can be done because the compiler already knows the
instruction set architecture. The EBC compiler cannot do that in the same way.
Instead, it generates code to calculate this value at execution time if the
result is different on different CPU architectures. This limitation means that
EBC code cannot use `sizeof (UINTN)`, `sizeof (INTN)`, and `sizeof (VOID *)`
(or other pointer types) in C language statements that require constant
expressions.

**********
**Note:** _The type_ **EFI_STATUS** _is required to by type_ **UINTN** _by the
UEFI Specification. This means that a variable of type_ **EFI_STATUS** _cannot
be used in C language statements that require constant expressions._ The code
in the following example **fails** when compiled for EBC.
**********

###### Example 251-Global Variable Initialization that fails for EBC

```c
#include <Uefi.h>
#include <UefiBootServicesTableLib.h>

//
// Global variable definitions
//
UINTN IntegerSize = sizeof (UINTN);        // EBC compiler error
UINTN PointerSize = sizeof (VOID *);       // EBC compiler error
EFI_STATUS Status = EFI_INVALID_PARAMETER; // EBC compiler error
```

The following example shows one method to address the EBC compiler errors in
the previous example. The general technique is to move the initialization of
global variables that are impacted by the EBC specific issue into the driver
entry point or other function that executes before the global variables are
used.

###### Example 252-Global Variable Initialization that works for EBC

```c
#include <Uefi.h>
#include <UefiBootServicesTableLib.h>

//
// Global variable definition
//
UINTN       IntegerSize;
UINTN       PointerSize;
EFI_STATUS  Status;

VOID
InitializeGlobals (
   VOID
  )
{
  IntegerSize = sizeof (UINTN);
  PointerSize = sizeof (VOID *);
  Status      = EFI_INVALID_PARAMETER;
}
```
