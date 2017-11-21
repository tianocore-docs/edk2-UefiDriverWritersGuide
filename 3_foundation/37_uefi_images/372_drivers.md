<!--- @file
  3.7.2 Drivers

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

### 3.7.2 Drivers

UEFI drivers are different from UEFI applications in that, unless there is an
error returned from the driver's entry point, the driver stays resident in
memory. The UEFI platform firmware, the boot manager, and UEFI applications may
load drivers.

#### 3.7.2.1 Boot service drivers

Boot drivers are loaded into memory marked as `EfiBootServicesCode`, and they
allocate their data structures from memory marked as `EfiBootServicesData`.
These memory types are converted to available memory after `ExitBootServices()`
is called.

#### 3.7.2.2 Runtime drivers

Runtime drivers are loaded in memory marked as `EfiRuntimeServicesCode`. They
allocate their data structures from memory marked as `EfiRuntimeServicesData`.
These types of memory are preserved after `ExitBootServices()` is called. This
preservation allows runtime driver to provide services to an operating system
while the operating system is running. Runtime drivers must publish an
alternative calling mechanism, because the UEFI handle database does not
persist into OS runtime. The alternative calling mechanism is
application-specific.

The most common examples of UEFI runtime drivers are the Floating Point
Software Assist driver (`FPSWA.efi`) and the network Universal Network Driver
Interface (UNDI) driver. The EDK II does include an UNDI driver. UEFI Drivers
for Network Interface Controllers (NICs) are discussed in detail in _Chapter
25_. Other runtime drivers are not common and are not discussed in this guide.

##### 3.7.2.2.1 Be rigorous when implementing runtime drivers

Implementing and validating runtime drivers is much more difficult than
implementing and validating boot service drivers. The difficulties occur
because UEFI supports the translation of runtime services and runtime drivers
from a physical addressing mode to a virtual addressing mode. For example, a
pointer might not have the same value in the physical address space as it might
in the virtual address space. Getting that translation, or mapping, correct is
very difficult because if even a single pointer translation is missed, the OS
may crash or hang if the runtime driver is called and a code path that accesses
that pointer is used. Debugging runtime services provides by UEFI Drivers at OS
runtime is more difficult than debugging UEFI Drivers in the preboot
environment. Since some code paths are executed infrequently, careful code
review and extensive validation of runtime drivers is strongly recommended.
Also, there are no utilities to perform such translations automatically. Each
piece of data and memory allocation must be inspected manually to determine if
it needs to be adjusted. That in itself can be an error-prone process.
Additionally, if another driver writer tries to adjust the code, that writer
might not be aware of each piece of data or memory allocation that must
adjusted.

There are best practices to help perform these translations. However, great
care must be taken to follow the recommended practices and UEFI requirements
rigorously. Many of the requirements for runtime drivers are listed in the
_UEFI Specification._ Make sure they are well understood. Of particular
importance are the sections on runtime services, and specifically, virtual
memory.
