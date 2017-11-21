<!--- @file
  4.2.1 Never Assume all UEFI Drivers are Executed

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

### 4.2.1 Never Assume all UEFI Drivers are Executed

Typically, the same vendor that produces a UEFI driver also produces an
OS-present driver for all the operating systems that the vendor chooses to
support. Because UEFI provides a mechanism to reduce the boot time by running
the minimum set of drivers that are required to connect the console and boot
devices, not all UEFI drivers may be executed on every boot. For example, the
system may have three SCSI cards but it only needs to install the driver on one
SCSI bus in order to boot the OS.

This minimum set of drivers means that the OS-present driver may be handed a
controller that may be in several different states. It may still be in the
power-on reset state, it may have been managed by a UEFI driver for a short
period of time and released, or it may have been managed by a UEFI driver right
up to the point in time where firmware hands control of the platform to the
operating system.

The OS-present driver must accept controllers in all of these states. This
acceptance requires the OS-present driver to make very few assumptions about
the state of the controller it manages.

**********
**Note:** _OS drivers shall not make assumptions that the UEFI driver has
initialized or configured the device in any way._
**********
**Note:** _I/O hot-plug does not involve UEFI driver execution, so the OS
driver must be able to initialize and operate the driver without UEFI support._
**********
