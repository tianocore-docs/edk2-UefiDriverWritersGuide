<!--- @file
  28.1 General notes about porting to IPF platforms

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

## 28.1 General notes about porting to IPF platforms

When porting to IPF platform, most developers take as much code as possible
that already exists and reuse it for the IPF platform. Unfortunately, some
developers porting code do not rigorously follow the UEFI conventions, such as
using only the data types defined in the Calling Conventions section of the
_UEFI Specification_. Others may not follow best coding practices. This is a
critical issue for IPF platforms because, although such code might work the
first time, it may fail a more complete set of validation tests. It is also
very likely that the code may not work when compiled with a different compiler,
or after another developer performs maintenance on the code.

* Use data types defined by the Calling Conventions section of the _UEFI
  Specification_.

* Use compiler flag settings that guarantee that the UEFI calling conventions
  for IPF are followed.

* If a UEFI driver contains assembly language sources for a different CPU
  architecture, then those sources must be converted to either IPF assembly
  language sources or to CPU agnostic C language sources. Conversion to C
  language sources is `recommended` and the EDK II library `BaseLib` and other
  EDK II libraries provide functions that may reduce or eliminate the need to
  assembly code in UEFI Drivers.

* Avoid alignment faults. This is the top issue in porting a UEFI driver to an
  IPF platform. Alignment faults may be due to type casting, packed data
  structures, or unaligned data structures.
