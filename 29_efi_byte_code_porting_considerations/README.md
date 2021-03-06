<!--- @file
  29 EFI Byte Code Porting Considerations

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

# 29 EFI Byte Code Porting Considerations

There are a few considerations to keep in mind when writing drivers that may be
ported to EBC (EFI byte code). This chapter describes these considerations in
detail and, where applicable, provides solutions to address them. If UEFI
drivers are implemented with these considerations in mind, the C code may not
require any changes. In this case, a native driver may be ported to EBC simply
by recompiling the driver sources using the Intel(R) C Compiler for EFI Byte
Code. The tasks required to convert a UEFI Driver to an EBC include the
following:

* Port assembly language sources to C language sources.

* Port C++ language sources to C language sources.

* Eliminate use of the float type.

* Convert floating point math operations to integer math operations.

* Eliminate use of sizeof() in statements that require a constant.

* Avoid arithmetic operations and comparisons between natural integers and
  fixed size integers. Some specific combinations produce unexpected results.

* Optimize for performance
