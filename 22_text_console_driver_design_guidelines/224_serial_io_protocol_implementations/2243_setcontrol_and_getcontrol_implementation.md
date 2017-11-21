<!--- @file
  22.4.3 SetControl() and GetControl() Implementation

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

### 22.4.3 SetControl() and GetControl() Implementation

`GetControl()` and `SetControl()` are used to view and modify respectively the
control bits on the serial device. All of the values listed in the following
table can be read back with `GetControl()`, but some cannot be modified with
`SetControl()`. If a non-modifiable bit is attempted to be set with
`SetControl()` then `EFI_UNSUPPORTED` must be returned.

The `Mode` pointer should be updated in this function when success has been
determined, but not modified if there is an error.

###### Table 35-Serial I/O protocol control bits

| **Control Bit #define**                   | **Modifiable with SetControl()** |
| ----------------------------------------- | -------------------------------- |
| `EFI_SERIAL_CLEAR_TO_SEND`                | NO                             |
| `EFI_SERIAL_DATA_SET_READY`               | NO                             |
| `EFI_SERIAL_RING_INDICATE`                | NO                             |
| `EFI_SERIAL_CARRIER_DETECT`               | NO                             |
| `EFI_SERIAL_REQUEST_TO_SEND`              | YES                            |
| `EFI_SERIAL_DATA_TERMINAL_READY`          | YES                            |
| `EFI_SERIAL_INPUT_BUFFER_EMPTY`           | NO                             |
| `EFI_SERIAL_OUTPUT_BUFFER_EMPTY`          | NO                             |
| `EFI_SERIAL_HARDWARE_LOOPBACK_ENABLE`     | YES                            |
| `EFI_SERIAL_SOFTWARE_LOOPBACK_ENABLE`     | YES                            |
| `EFI_SERIAL_HARDWARE_FLOW_CONTROL_ENABLE` | YES                            |
