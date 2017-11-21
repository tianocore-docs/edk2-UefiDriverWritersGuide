<!--- @file
  3.7.1 Applications

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

### 3.7.1 Applications

A UEFI application starts execution at its entry point and then executes until
it returns from its entry point or it calls the `Exit()` boot service function.
When done, the image is unloaded from memory. It does not stay resident. Some
examples of common UEFI applications include the following: - UEFI Shell

* UEFI Shell Applications

* Flash utilities

* Diagnostic utilities

It is perfectly acceptable to invoke UEFI applications from inside other UEFI
applications.

#### 3.7.1.1 OS loader

The _UEFI Specification_ details a special type of UEFI application called an
_OS boot loader._ It is a UEFI application that calls `ExitBootServices()`.
`ExitBootServices()` is called when the OS loader has set up enough of the OS
infrastructure that it is ready to assume ownership of the system resources. At
`ExitBootServices()`, the UEFI platform firmware frees all of its boot time
services and boot time drivers, leaving only the runtime services and runtime
drivers.
