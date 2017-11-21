<!--- @file
  3.8.2 Creating locks

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

### 3.8.2 Creating locks

It is possible for the code in normal context and the code in interrupt context
(i.e. notification functions) to access the same data structure. This is
because UEFI _does_ support a single timer interrupt. This access can cause
issues if the updates to a shared data structure are not atomic. A UEFI
application or UEFI driver that wants to guarantee exclusive access to a shared
data structure can temporarily raise the task priority level to prevent
simultaneous access from both normal context and interrupt context. A lock can
be created by temporarily raising the task priority level to `TPL_HIGH_LEVEL`.
This level blocks even the one timer interrupt. However, care must be taken to
minimize the amount of time that the system executes at `TPL_HIGH_LEVEL`. All
timer-based events are blocked during this time and any driver requiring
periodic access to a device is prevented from accessing its device. See the
Boot Services chapter of the _UEFI Specification_ for more information on Task
Priority Levels and _Section 5.1.4_ of this guide for examples on how Task
Priority Levels can be used to create and manage locks.
