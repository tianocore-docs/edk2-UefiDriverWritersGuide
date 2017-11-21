<!--- @file
  4.1.3 Maximize Warning Levels

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

### 4.1.3 Maximize Warning Levels

To catch possible issues with assigning or comparing values of different sizes,
UEFI drivers should always be compiled with the highest warning level possible.
For example, the Microsoft* compilers support the /WX and /W3 or /W4
compiler flags. The /WX flag causes any compile time warnings to generate an
error, so the build stops when a warning is generated. The /W3 and /W4 flags
set the warning level to 3 and 4 respectively. At these warning levels, any
size mismatches in assignments and comparisons generate a warning. With the /WX
flag, the compile stops when such size mismatches are detected.

If a UEFI Driver is being developed for a 32-bit architecture and is expected
to be ported to a 64-bit architecture, it is a good idea to compile the UEFI
driver with a 64bit compiler during the development process. This helps ensure
the code is clean when validation on the 64-bit processor is begun. By using
the /WX and /W3 or /W4 compiler flags, any size mismatches that are generated
by only 64-bit code are detected.

**********
**TIP:** As the warning levels are increased, a compiler may produce more error
messages. This helps develop more robust, portable code.
**********
