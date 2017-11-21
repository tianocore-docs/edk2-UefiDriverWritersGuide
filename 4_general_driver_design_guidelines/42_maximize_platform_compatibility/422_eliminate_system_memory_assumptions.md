<!--- @file
  4.2.2 Eliminate System Memory Assumptions

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

### 4.2.2 Eliminate System Memory Assumptions

Do not make assumptions about the system memory configuration, including memory
allocations and memory that is used for DMA buffers. There may be unexpected
gaps in the memory map in any system and entire memory regions may be missing.
For example, some memory regions could already be allocated (such as for an I/O
device), some memory may be non-addressable, and/or physical memory could
actually be missing. UEFI is designed for a wide variety of platforms. As such,
portable drivers should not have hard-coded limits. Instead, they should rely
on published specifications, UEFI, and the system firmware to provide them with
the platform limitations and platform resources, including the following:

* The number of adapters that can be supported in a system

* The type of adapter that can be supported on each bus

* The available memory resources

In addition, drivers should not make assumptions on a platform. Instead, they
should make sure they support all the cases that are allowed by the _UEFI
Specification_. For example, memory is not always available beneath the 4 GB
boundary (some systems may not have any memory under 4 GB at all) and drivers
have to be designed to be compatible with these types of system configurations.
As another example, some systems do not support PC-AT(R) legacy hardware and
your drivers should not expect them to be present.
