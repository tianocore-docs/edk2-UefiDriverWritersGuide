<!--- @file
  19.3.4 State machine consideration

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

### 19.3.4 State machine consideration

To implement USB device support, the USB device drivers must maintain a state
machine for their own transaction process. For example, the USB mass storage
driver must maintain a tri-state machine, which contains
Command->[Data]->Status states.

It should work well because it looks like a handshake process that is designed
to be error free. Maintaining this state machine should provide robust error
handling.

However, imagine the following situation:
* A command is sent to the device that the host needs some data from the device.
* The device's response is too slow and it keeps NAK in its data endpoint.
* The host sees the NAK so many times that it thinks there is no data available
  from the device. It timeouts this data-phase operation.
* The state machine is then in the status phase. It asks for the status data
  from the device.
* The device then sends the real data-phase data to the host.
* The host cannot understand the data from the device as status data, so it
  resets the device and retries the operation.
* The necessary components of a dead loop then exist. The final result is a
  system likely to hang, an unusable device, or both.

How can this situation be avoided? If the device keeps NAK, then sooner or later the data becomes available and no assumption can be made about the data's availability. There are some cases in which the device's response is so slow that the timeout is not enough for it to get data ready. As a result,
retrying the transaction in the data phase may be necessary.

**********
**TIP:** Make sure USB device drivers maintain a state machine for their own
transaction process. The driver might need to retry transactions in the data
phase in order to avoid dead loops and other errors.
**********
