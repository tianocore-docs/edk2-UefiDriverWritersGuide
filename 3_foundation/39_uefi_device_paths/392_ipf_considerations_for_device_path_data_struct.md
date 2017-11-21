<!--- @file
  3.9.2 IPF Considerations for device path data structures

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

### 3.9.2 IPF Considerations for device path data structures

Individual device paths nodes may be any length, and each device path node in a
complete device path starts immediately after the previous device path node.
This means that device path nodes inside of a full device path may not start on
a naturally aligned boundary. This can cause problems for CPU architectures
that do not support unaligned memory accesses such as IPF. A device path node
that is not a multiple of 8 bytes in length may cause a device path node that
follows to be unaligned. Implementing source code that manages device paths
requires some special techniques to guarantee that the source code is portable
to all the CPU architectures supported by the _UEFI Specification_.

**********
**TIP:** Be careful when using device paths. Make sure an alignment fault is
not generated.
**********

See _Chapter 4_ in this guide for more information about architecture-specific
considerations. Refer to _Chapter 28_ for IPF platform porting considerations.
