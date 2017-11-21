<!--- @file
  19.5 Nonconforming USB Devices

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

## 19.5 Nonconforming USB Devices

There are debates on how best to handle devices that do not conform to the _USB_
_Specification._ It is recommended that the driver stack comply with the _USB_
_Specification_ and reject any nonconforming devices. A nonconforming device
that is not linked into the USB software stack should not interact further with
the system.

However, even if the device is nonconforming and the USB driver stack should
reject it, developers need to make sure that the nonconforming device does not
cause system failures. The developer must not make any assumptions about the
device's behavior, especially since, once a system is known not to conform, its
behavior cannot be trusted. It can respond to addressing that was not meant for
that device; it can corrupt data going into it and coming back from it; and it
cannot be trusted to perform its intended function(s). It is essential for the
end-user's experience that the nonconforming device does not negatively affect
the system.

A driver can only reliably reject nonconforming devices that it already knows
about. For USB devices, the identity of devices may be determined by use of the
data in the USB device description packets.

USB devices have several sets of known issues that may be detected and hidden
from the user. For example, some keyboards auto-repeat when keys are pressed
for an extended period of time. In this case the consuming driver should simply
ignore packets which repeatedly provide identical information. Media devices
also have several issues. USB requires implementation of the SCSI or ATAPI
specifications, which, for many e.g. thumb drives, is beyond their capacity. As
such, relying only on basic commands can greatly increase the probability of
functionality.
