<!--- @file
  22.3.4 QueryMode() Implementation

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

### 22.3.4 QueryMode() Implementation

The `QueryMode()` function returns information supported modes. The UEFI Driver
is required to return the number of _Rows_ and number of _Columns_ for each
supported _ModeNumber_. _ModeNumber_ must be less than `Mode->MaxMode`.

**********
**Note:** _All devices that support the Simple Text Output Protocol must
minimally support an 80 x 25 character mode. Additional modes are optional.
This means a basic Simple Text Output Protocol implementation supports a
single_ _ModeNumber_ _of 0 with a geometry of 80_ _Columns_ _and 25_ _Rows_,
_and reports a_ **Mode->MaxMode** _value of 1._
**********

The `QueryMode()` function is typically used one of two ways:

1\. Query for the geometry of the current mode. The _following line_ populates
the _Columns_ and _Rows_ variables with the geometry of the currently active
console output.

###### Example 228-Query current Simple Text Output Mode

```c
#include <Uefi.h>
#include <Protocol/SimpleTextOut.h>

EFI_STATUS Status;
EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL *SimpleTextOutput;
UINTN Columns;
UINTN Rows;

Status = SimpleTextOutput->QueryMode (
                             SimpleTextOutput,
                             SimpleTextOutput->Mode->Mode,
                             &Columns,
                             &Rows
                             );
```

2\. Loop through all valid geometries that a given console can support. The
following line populates (repeatedly) the _Column_ and _Row_ variables with the
geometry of the each supported output mode.

###### Example 229-Query all Simple Text Output Modes

```c
#include <Uefi.h>
#include <Protocol/SimpleTextOut.h>

EFI_STATUS Status;
UINTN Index;
EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL *SimpleTextOutput;
UINTN Columns;
UINTN Rows;

for (Index = 0 ; Index < SimpleTextOutput->Mode->MaxMode ; Index++) {
  Status = SimpleTextOutput->QueryMode (
                               SimpleTextOutput,
                               Index,
                               &Columns,
                               &Rows
                               );
}
```
