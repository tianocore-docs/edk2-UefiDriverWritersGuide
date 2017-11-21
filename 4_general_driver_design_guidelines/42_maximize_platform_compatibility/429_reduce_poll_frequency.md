<!--- @file
  4.2.9 Reduce Poll Frequency

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

### 4.2.9 Reduce Poll Frequency

UEFI drivers operate in a polled mode and do not use interrupts. For example,
UEFI drivers that implement blocking I/O services can simply poll the device
until the request is complete. UEFI drivers that implement non-blocking I/O can
create a periodic timer event to poll a device at periodic intervals.

A common mistake in UEFI drivers is polling too often.

Remember that polling, versus interrupts, is a pull model, not a push model.
The tradeoff in a polling system is how fast the device is polled (which can
degrade system performance) versus how responsive the driver is to that
request. For example, in a polling system, the driver should not send a request
to a device and wait until that device responds before moving on to another
task. In general, the polling interval should be set to the largest possible
period for the UEFI driver to complete its I/O services in a reasonable period
of time. The overall performance of a UEFI-enabled platform degrades if too
many UEFI drivers create high-frequency periodic timer events.

**********
**Note:** _It is recommended that the period of a periodic timer event be at
least 10 ms. In general, the period should be as large as possible based upon a
specific device's timing requirements. Most drivers can use events with timer
periods in the range of 100 ms to several seconds._
**********

When initially writing the driver, an estimate can be made for the initial
polling frequency. However, the polling frequency may have to be adjusted based
on an analysis of the driver's performance on an actual machine.

**********
**TIP:** As part of the development process, make sure time is reserved for
performance analysis to find out how much time is taken up polling each device.
**********

#### 4.2.9.1 Distinguishing a polling issue versus another type of bug

The symptoms of a polling issue versus some other type of bug can look nearly
identical. The key to identifying a polling issue is: Don't assume anything.
Begin simply by performing an analysis to get data-the time taken by each task
can be measured. If a task is taking longer than expected, the code associated
with that task can then be examined more closely.
