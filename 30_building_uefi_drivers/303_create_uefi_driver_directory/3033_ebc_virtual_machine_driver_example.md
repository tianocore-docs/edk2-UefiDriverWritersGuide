<!--- @file
  30.3.3 EBC Virtual Machine Driver Example

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

### 30.3.3 EBC Virtual Machine Driver Example

The following example shows the directory structure for another driver that
includes instruction set architecture-specific files for three of the supported
instruction sets. The files `EbcLowLevel.asm` and `EbcLowLevel.s` contain logic
that must be implemented in assembly to handle the transitions between native
execution and the EBC interpreter. Doing so makes the driver work on all three
supported architectures, but the UEFI driver takes longer to develop and is
more difficult to maintain if any changes are required in the instruction set
architecture-specific components.

If possible, a UEFI driver should be implemented in C with no instruction set
architecture-specific files, which reduces the development time, reduces
maintenance costs, and increases portability.

###### Example 262-EBC driver with instruction set architecture-specific files

```
MdeModulePkg\ Universal\ EbcDxe\
EbcDxe.inf
EbcExecute.c
EbcExecute.h EbcInt.c
EbcInt.h
Ia32\
EbcSupport.c
EbcLowLevel.asm
EbcLowLevel.S Ipf\
EbcSupport.c
EbcSupport.h
EbcLowLevel.s X64\
EbcSupport.c
EbcLowLevel.asm
EbcLowLevel.S
```
