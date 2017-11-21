<!--- @file
  3.15.12 Boot Manager Boot Option Processing

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

### 3.15.12 Boot Manager Boot Option Processing

The platform boot manager displays the boot option menu and if the auto-boot
_TimeOut_ environment variable has been set, then the first boot option is
loaded when the timer expires. The boot options can be enumerated by the
platform boot manager by reading the _BootOrder_ and _Boot####_ environment
variables. These environment variables are more thoroughly discussed in the
Boot Manager chapter of the _UEFI Specification_. A boot option is typically an
OS loader that never returns to UEFI, but boot options can also be UEFI
applications like diagnostic utilities or the UEFI Shell.

If a boot option does return to the platform boot manager, and the return
status is not `EFI_SUCCESS`, then the platform boot manager processes the next
boot option. This process is repeated until an OS is booted, `EFI_SUCESS` is
returned by a boot option or the list of boot options is exhausted. Once the
boot process has halted, the platform boot manager may provide a user interface
that allows the user to manually boot an OS or manage the platform.

The platform boot manager uses the device path in each boot option to ensure
that the device required to access the boot option has been added to the UEFI
handle database. This process is exactly the same as the process used for the
console variables _ErrOut_, _ConOut_, and _ConIn_.
