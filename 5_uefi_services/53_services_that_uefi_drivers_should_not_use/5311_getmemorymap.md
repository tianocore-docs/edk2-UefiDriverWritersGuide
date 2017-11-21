<!--- @file
  5.3.11 GetMemoryMap()

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

### 5.3.11 GetMemoryMap()

UEFI drivers should not use this service because UEFI drivers should not depend
upon the physical memory map of the platform. The `AllocatePool()` and
`AllocatePages()` services allow a UEFI driver to allocate system memory. The
`FreePool()` and `FreePages()` services allow an UEFI driver to free previously
allocated memory.

If there are limitations on the memory areas that a specific device may use,
then those limitations should be managed by a parent I/O abstraction that
understands the details of the platform hardware.

For example, PCI device drivers should use the services of the PCI I/O Protocol
to manage DMA buffers. The PCI I/O Protocol is produced by the PCI bus driver
that uses the services if the PCI Root Bridge I/O Protocol to manage DMA
buffers. The PCI Root Bridge I/O Protocol is chipset and platform specific, so
the component that produces the PCI Root Bridge I/O Protocol understands what
memory regions can be used for DMA operations. By pushing the responsibility
into the chipset- and platform-specific components, the PCI device drivers and
PCI bus drivers are easier to implement and are portable across a wide variety
of platforms.

This service is typically used by a UEFI OS Loader to retrieve the memory map
just before the OS takes control of the platform by calling
`ExitBootServices()`. It may also be used by UEFI applications, such as
diagnostics or debug utilities, to show how platform memory has been allocated.
