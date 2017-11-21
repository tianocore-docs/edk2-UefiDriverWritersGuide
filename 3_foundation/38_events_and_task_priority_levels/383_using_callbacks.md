<!--- @file
  3.8.3 Using callbacks

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

### 3.8.3 Using callbacks

The calls to create an event take two important parameters: the callback and
the parameter pointer.

The callback function is invoked when the event occurs. Using callbacks
appropriately is not difficult-as long as the following rules are followed:

* The parameter pointer can point to any static (not on the stack) structure.
  The parameter pointer is used to provide state information for the event
  invocation. The parameter pointer is particularly useful if multiple events
  must be handled by the same callback.

* The callback function, when invoked, may only assume its priority level, its
  parameter pointer, and that it has a stack. It must derive all context from
  the parameter pointer and the static data left in its module. This makes
  writing callbacks somewhat more challenging than normal driver code.

#### 3.8.3.1 Debugging callbacks

Debugging callbacks is a little like debugging interrupt handlers in that one
is not always sure when a callback is invoked. Most normal debugging facilities
function as expected in callbacks.

There can be a temptation to write one's driver as a series of callbacks. This
is not recommended since normal code is easier to debug, and managing a large
number of the context structures addressed by parameter pointers becomes
difficult to maintain.

**********
**TIP:** Minimize the use of callbacks. Only use a callback when an operation
cannot be implemented as part of UEFI Driver initialization or through a
protocol services provided by the UEFI Driver.
**********
