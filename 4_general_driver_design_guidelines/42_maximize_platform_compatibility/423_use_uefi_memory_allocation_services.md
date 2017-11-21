<!--- @file
  4.2.3 Use UEFI Memory Allocation Services

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

### 4.2.3 Use UEFI Memory Allocation Services

The `AllocatePool()` service _does not_ allow the caller to specify a preferred
address so this service is always safe to use and has no impact on platform
compatibility. The `AllocatePages()` service _does_ have a mode that allows a
specific address to be specified or a range of addresses to be specified. The
allocation type of `AllocateAnyPages` is safe to use and increases platform
compatibility. The allocation types of `AllocateMaxAddress` and
`AllocateAddress` may reduce platform compatibility. Refer to _Chapter 5_ in
this guide for information about using the AllocatePages service.

The general guideline for UEFI drivers is to make as few assumptions about the
memory configuration of the platform as possible. This guideline applies to the
memory that a UEFI driver allocates and the DMA buffer addresses that DMA bus
master's use. A UEFI driver should not allocate buffers from specific addresses
or below specific addresses. These types of allocations may fail on different
system configurations. The following rules help ensure a UEFI Driver makes
appropriate memory allocations.

* Use natural alignment (byte values on byte boundaries) when allocating
  buffers. This maximizes portability and helps avoid alignment faults on IPF
  platforms.

* Buffers allocated on a 32-bit CPU architecture using the UEFI Boot Service
  AllocatePool()are guaranteed to be below 4GB.

* Buffers allocated on a 64-bit CPU architecture using the UEFI Boot Service
  AllocatePool() may be above 4GB if memory is present above 4 GB.

* The UEFI Boot Service AllocatePages() may be used to allocate a buffer
  anywhere system memory is present. This means AllocatePages() may return a
  buffer on a 32-bit CPU architecture that is above 4 GB if memory is present
  above 4GB and that buffer can never be accessed.

* All UEFI drivers must be aware that pointers may contain values above 4 GB,
  and care must be taken never to strip the upper address bits.

* To prevent memory leaks, every allocation operation must have a corresponding
  free operation.

* Test UEFI drivers on 64-bit architectures with memory configurations where
  system memory is present above 4GB.

* Test UEFI drivers on 64-bit architectures with memory configurations where
  system memory is not present above 4GB.

* UEFI drivers should not use fixed-size arrays. Instead, memory resources
  should be dynamically allocated using the `AllocatePages()` and
  `AllocatePool()` services.
