<!--- @file
  3.15.3 Connecting consoles

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

### 3.15.3 Connecting consoles

At this point during the platform initialization, the firmware has not
initialized or configured a "console" device that allows user input. This
absence is often because a PCI device, waits for the PCI bus driver to provide
device handles for the console(s).

Most UEFI conformant platforms follow a console connection strategy to connect
the consoles in a manner consistent with that of the platform. This ensures
that the platform is able to display messages to all of the selected consoles
through the standard UEFI mechanisms. Initially, this includes platform
initialization and informational screens, and later (during setup), HII
functionality and forms. Prior to this point, platform messages, if any, are
conveyed through platform-specific methods.

**********
**Note:** _During initialization, the platform needs to connect console devices
to the driver. HII functionality is about displaying configurable information
to the user, which happens after consoles are initialized and after an HII
compatible setup engine is invoked. UEFI Drivers should never directly access
console devices except for the few UEFI driver related services that explicitly
allow user interaction. In most cases, UEFI drivers use HII infrastructure to
present information to users._
**********
