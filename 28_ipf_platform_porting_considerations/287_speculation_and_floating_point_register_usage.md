<!--- @file
  28.7 Speculation and floating point register usage

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

## 28.7 Speculation and floating point register usage

IPF platforms support speculative memory accesses and a large number of
floating point registers. UEFI drivers that are compiled for IPF platforms must
follow the calling conventions defined in the _SAL Specification._ The _SAL
Specification_ only allows the first 32 floating point registers to be used and
defines the amount of speculation support that a platform is required to
implement for the UEFI pre-boot environment. These requirements mean that the
correct compiler and linker switches must be set correctly to guarantee that
these calling conventions are followed. The EDK II provides proper compiler and
linker settings for several tool chains that support IPF platforms. These
settings may have to be adjusted if updates to a tool chain are release or if a
different tool chain is used. The following table shows the compiler flags for
a few different compilers. The compiler flag that specifies that only the first
32 floating point registers may be used for Microsoft* compilers is
`/QIPF_fr32`. The equivalent flag of Intel compilers is `/QIA64_fr32`.
