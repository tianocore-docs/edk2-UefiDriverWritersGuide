<!--- @file
  30.3.2 Reserved Directory Names

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

### 30.3.2 Reserved Directory Names

The UEFI Drivers in the two examples above do not contain any instruction set
architecture-specific files. This absence means that this driver is designed to
be portable between all CPU architecture supported by the _UEFI Specification_.
If a UEFI Driver requires instruction set architecture-specific source files,
then those source files are typically placed in subdirectories below the UEFI
driver's main directory in an EDK II package. A separate subdirectory is
required for each instruction set architecture that the UEFI Driver supports.
The table below lists the directory names that are reserved for the instruction
set architecture-specific files.

###### Table 39-Reserved directory names

| **Directory name** | **Notes**                                 |
| ------------------ | ----------------------------------------- |
| Ia32               | May contain `.c`, `.h`, and `.asm` files. |
| Ipf                | May contain `.c`, `.h`, and `.s` files.   |
| Ebc                | May contain `.c` and `.h` files.          |
| X64                | May contain `.c`, `.h`, and `.asm` files. |

**********
**Note:** _Code written in EBC is still C language code, and the sources look
the same as for any other driver. It is when the compiler converts it from a
high-level language (C) to object code (EBC versus native code) that the
difference becomes evident._

_For EBC, the object code generated is not native to the processor but rather
is pseudo-object-code that looks like RISC processor object code. That code is
fed into an interpreter, which interprets each instruction and acts upon it.
The EBC output is not 32-bit or 64-bit-based, but rather conforms to its own
standard. Thus a system with a valid interpreter for its architecture can
translate EBC compiled code into operational instructions on any given
architecture._
**********
