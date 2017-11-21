<!--- @file
  3.1 Basic programming model

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

## 3.1 Basic programming model

Common questions about UEFI include:

* How are programs in UEFI implemented?

* What makes UEFI programming different from an operating system?

* What makes UEFI different from other firmware environments?

* In particular, what is the programming model for a UEFI Driver?

Key points about writing UEFI-conformant drivers are that:

* UEFI Drivers are relocatable PE/COFF images whose format is defined by the
  Microsoft Portable Executable and Common Object File Format Specification.

* UEFI Drivers may be compiled for any of the CPU architectures supported by
  the _UEFI Specification_.

* UEFI Drivers run on a single CPU thread.

* The driver support infrastructure does not extend beyond the boot processor.

* Drivers sit above some interfaces (for example, bus abstractions) and below
  other interfaces: They are both consumers and producers. The _UEFI
  Specification_ defines the interfaces and they are extensible.

* Each driver is expected to cooperate with other drivers, other modules and
  the underlying core services.

* The communicating modules bind together to create stacks of cooperating
  drivers to accomplish tasks.

* Inter-module communication is enabled via interfaces known as protocols and
  via events.

* Tables provided at invocation provide access to core services.

* The operating environment is non-preemptive and polled. There are no tasks
  per se. Instead, modules execute sequentially.

* There is only one interrupt: the timer. This means that data structures
  accessed by both in-line code and timer-driven code must take care to
  synchronize access to critical paths. This is accomplished via privilege
  levels.
