<!--- @file
  12.6.1 Minimize callbacks

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

### 12.6.1 Minimize callbacks

There are circumstances in which a callback is required. For example, callbacks
are necessary when real-time data such as a temperature or voltage is required,
or when direct password input is required to unlock a security feature.

However, the callback is useful for an extremely limited number of
circumstances and can be used inappropriately.

**********
**Caution:** It is very important with UEFI drivers that the use of callbacks
is minimized. The use of_callbacks can significantly slow down a browser. Callbacks tend to be hard to maintain and are also typically very buggy. They don't adapt well to various video forms, which becomes an issue for interoperability between different types of devices. Finally, they cannot be used remotely, which creates significant problems with remote management of drivers._
**********

There are a number of useful techniques to reduce the use of callbacks. For
example, use the rich set of comparison and calculation operators in VFR to
validate input rather than resorting to callbacks. Also, modify the IFR (the
language into which VFR compiles) before handing the IFR to HII. This allows
the IFR to be adapted to the state of the system as the driver finds it. For
example, don't use callbacks to determine attacked devices. Instead, determine
the devices when providing the HII and fill in the data into the VFR.

Note that the HII engine can also do some testing of values, such as for
minimum and maximum limits―a callback is not required for these operations.
Instead, these checks are incorporated into the VFR sources, and the HII engine
checks perform the tests against the minimum and maximum values. String
compares may also be performed without the use of a callback.

**********
**TIP:** Use a callback only when absolutely required, and when no other
methods are available to perform the task. Almost nothing should be a callback.
**********
**TIP:** Use callbacks only for dynamically changing data. Do not use callbacks
for static data.
**********
**TIP:** Do not use callbacks to format tables or make the interface look nice.
**********
**TIP:** Do not make assumptions about the way the data returned from the
callback is displayed.
**********

Basically, let the HII engine perform as much of the work as possible and
rigorously minimize the use of callbacks.

#### 12.6.1.1 Callbacks create issues with remote configuration

One of the biggest issues with remote configuration is the use of callbacks
(see the previous discussion for more information). For example, if
configuration changes must be made to thousands of systems at a remote site,
callback functions cannot be used, because the remote systems may be powered
down or otherwise unavailable.

**********
**TIP:** Use a callback only when absolutely required.
**********

#### 12.6.1.2 Callbacks create issues with interoperability

Callbacks are also an issue with regards to interoperability of remote devices.
For example, a server might have a 32x4 plasma display. A browser may be
implemented for VFR to support a 32x4 display, but the callback functions
typically do not function well between device types. If a UEFI Driver is
intended to be used in remote configuration scenarios, then avoid the use of
callbacks.
