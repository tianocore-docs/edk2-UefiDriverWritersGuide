<!--- @file
  3.6.4 Multiple protocol instances

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

### 3.6.4 Multiple protocol instances

Multiple protocols are installed on the same handle if the protocols provide
services related to that one handle. There are several handle types. The most
common are image handles and device handles. For example, if there are multiple
I/O services for a single device that are abstracted through multiple
protocols, then multiple protocols must be installed onto the handle for that
device.

A handle may have many protocols attached to it. However, it may have only one
protocol of each GUID name. In other words, a single handle may not produce
more than one instance of any single protocol. This prevents nondeterministic
behavior about which instance would be consumed by a given request.

However, drivers may create multiple "instances" of a particular protocol and
attach each instance to a different handle. This scenario is the case with the
PCI I/O Protocol, where the PCI bus driver installs a PCI I/O Protocol instance
for each PCI device. Each "instance" of the PCI I/O Protocol is configured with
data values unique to that PCI device, including the location and size of the
UEFI-conformant Option ROM (OpROM) image.

Each driver can install customized versions of the same protocol (as long as it
is not on the same handle). For example, each UEFI driver produces the
Component Name Protocols on its driver image handle, yet when the Component
Name Protocols' `GetDriverName()` function is called, each handle returns the
unique name of the driver that owns that image handle. The `GetDriverName()`
function on the USB bus driver handle returns "USB bus driver" for the English
language, but the `GetDriverName()` function on the PXE driver handle returns
"PXE base code driver."
