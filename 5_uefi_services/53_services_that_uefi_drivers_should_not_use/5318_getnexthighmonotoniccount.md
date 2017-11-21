<!--- @file
  5.3.18 GetNextHighMonotonicCount()

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

### 5.3.18 GetNextHighMonotonicCount()

There are no use cases for this service by a UEFI Driver. It should never be
called.

This service is only used by operating systems to manage the upper 32-bits of
the 64-bit monotonic counter after the operating system has called
`ExitBootServices()`. An operating system that chooses to use the UEFI provided
64-bit monotonic counter should acquire the value of the 64-bit monotonic
counter before `ExitBootServices()` using the UEFI Boot Service
`GetNextMonotonicCount()`. The operating system can manage the volatile lower
32-bits of the 64-bit monotonic counter on its own. If a 32bit rollover
condition occurs, then the operating system can use the UEFI Runtime Service
`GetNextHighMonotonicCount()` to increment the upper 32-bits of the 64-bit
monotonic counter. The upper 32-bits are non-volatile and it is the
responsibility of the UEFI firmware to guarantee that the upper 32-bits of the
64-bit monotonic counter are preserved across system resets and power cycles.
