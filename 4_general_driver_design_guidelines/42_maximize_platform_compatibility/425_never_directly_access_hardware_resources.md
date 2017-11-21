<!--- @file
  4.2.5 Never Directly Access Hardware Resources

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

### 4.2.5 Never Directly Access Hardware Resources

A UEFI driver should also never directly access any system chipset resources.
Directly accessing these resources limits the compatibility of the UEFI driver
to systems only with that specific chipset.

Instead, the UEFI boot services, UEFI runtime services, and various protocol
services should be used to access the system resources that are required by a
UEFI driver. The driver should look only for services to hook into-a
capability, such as a PCI bus. The driver then consumes the protocols necessary
for accessing that capability. It is the chipset's responsibility to get that
capability ready for use.

**********
**TIP:** The general rule is to only access the hardware that the UEFI Driver
supports and use abstractions for other hardware. If there is not an
abstraction for a system device, do not use the device. That device may change
interface and functionality in the future.
**********

This recommendation serves several purposes. By using the software abstractions
provided by the platform vendor, the UEFI driver maximizes its platform
compatibility. The platform vendor can also optimize the services that are
provided by the platform, so the performance of the UEFI driver improves by
using these services. _Chapter 29_ in this guide discusses the EBC porting
considerations, and one of the most important considerations is the performance
of an EBC driver because EBC code is interpreted. The performance of an EBC
driver can be greatly improved by calling system services instead of using
internal functions.

Putting effort into source code portability helps maximize future platform
compatibility.
