<!--- @file
  28.2 Alignment Faults

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

## 28.2 Alignment Faults

The single most common issue with UEFI drivers for IPF platforms is alignment.
Alignment faults cannot occur on IA32, X64, or EBC platforms, but can occur on
IPF platforms. The IPF platform requires that all transactions be performed
only on natural boundaries. This requirement means that a 64-bit read or write
transaction must begin on an 8-byte boundary, a 32-bit read or write
transaction must begin on a 4-byte boundary, and a 16-bit read or write
transaction must begin on a 2-byte boundary.

In most cases, the driver writer does not need to worry about this issue
because the C compiler guarantees that accessing global variables, function
parameters, local variables, and fields of data structures do not cause
alignment faults.

Alignment faults can be generated when:

* C code can generate an alignment fault when a pointer is cast from one type
  to another or when packed data structures are used.

* Data structures are declared to be byte packed using `#pragma pack(1)` or
  equivalent.

* Assembly language can generate an alignment fault, but it is the assembly
  programmer's responsibility to ensure alignment faults are not generated.
  This topic is not covered further in this guide.
