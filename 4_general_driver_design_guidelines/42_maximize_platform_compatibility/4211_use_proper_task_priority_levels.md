<!--- @file
  4.2.11 Use Proper Task Priority Levels

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

### 4.2.11 Use Proper Task Priority Levels

The TPLs provide a mechanism for code to run at a higher priority than
application code. One can be running the UEFI Shell, and a UEFI device driver
can have a timer event fire and gain control to go poll its device. The
`TPL_CALLBACK` level is typically used for deferred software calls and
`TPL_NOTIFY` is typically used by device drivers. `TPL_HIGH_LEVEL` is typically
used for locks on shared data structures.

Drivers may use events and TPLs if they perform non-blocking I/O. If they
perform blocking I/O, then events are not used. They may still use the
`RaiseTPL()` and `RestoreTPL()` for critical sections.

Driver diagnostics are typically just applications. They do not normally need
to use TPLs or events unless the diagnostics is testing the TPL or event
mechanisms in EFI. However, there is one exception. If a diagnostic needs to
guarantee that EFI's timer interrupt is disabled, then the diagnostic can raise
the TPL to `TPL_HIGH_LEVEL`. If this level is required, it should be done for
the shortest possible time interval.

**********
**Caution:** There are ways in which the platform firmware can be put into an
undefined state by misuse of the `RaiseTPL()` and `RestoreTPL()` functions.
**********
**Caution:** Do not misuse the `RaiseTPL()` service by raising the task
priority level too high for an extended period of time. Raising the TPL level
above TPLAPPLICATION circumvents the timer tick. This can interfere with other
drivers, applications, and other elements that rely on the timer tick. It can
cause extreme, and sometimes catastrophic slowing of the system. It can cause
other drivers, applications, and other things that rely on the timer tick to
fail. Always mirror the raise TPL service with the restore TPL service.
**********
