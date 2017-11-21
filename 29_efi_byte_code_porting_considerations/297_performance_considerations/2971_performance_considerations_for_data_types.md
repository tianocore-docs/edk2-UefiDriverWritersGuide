<!--- @file
  29.7.1 Performance considerations for data types

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

### 29.7.1 Performance considerations for data types

Avoid declaration and initialization of variables or structures that contain
native length data types such as `INTN`, `UINTN`, and pointers. One of the
issues with initializing variables occurs during optimization. If variables are
initialized statically, the compiler optimizes them for size and, for example,
gives the variable a 32-bit placement or a 16-bit placement. This can create
problems if the variables are a size that is different on different CPU
architectures.

**********
**TIP:** Initialize variables separately from declaring them.
**********

The amount of variable initialization that is performed during EBC runtime
initialization can be determined by viewing the PE/COFF sections of a UEFI
Driver compiled for EBC. The linker provided with Microsoft* tools provides
a method to perform this operation.

The command is: `link -dump -headers <filename>`. This command dumps the different parts of an .EFI file. The goal is to minimize the _VARBSS_ section while maximizing the .data and .rdata sections of the
PE/COFF image.
