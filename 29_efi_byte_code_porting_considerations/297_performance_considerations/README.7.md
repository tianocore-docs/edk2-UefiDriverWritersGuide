<!--- @file
  29.7 Performance considerations

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

## 29.7 Performance considerations

All EBC executables require an EBC virtual machine interpreter to be executed.
Because all EBC executables are running through an interpreter, they execute
slower than native UEFI executables. As a result, a UEFI driver that is
compiled with an EBC compiler should be optimized for performance to improve
the usability of the UEFI Driver. _Chapter 4_ covers speed optimization
techniques that may be used to improve the performance of all UEFI Drivers.

The simplest way to maximize the speed of a UEFI Driver compiled for EBC is to
maximize the use of UEFI Boot Services, UEFI Runtime Services, and protocols
produced by other UEFI components. These calls outside of the UEFI Driver
compiled for EBC help improve performance because those other services may be
native calls that can be executed without the overhead of the EBC virtual
machine interpreter. If all UEFI Drivers compiled for EBC follow the
recommendation, even if one UEFI Driver compiled for EBC calls another UEFI
Driver compiled for EBC, the overhead of the EBC interpreter is still minimized.
