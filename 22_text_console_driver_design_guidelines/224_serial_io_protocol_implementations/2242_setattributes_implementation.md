<!--- @file
  22.4.2 SetAttributes() Implementation

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

### 22.4.2 SetAttributes() Implementation

The `SetAttributes()` function is used by the caller to change the serial
connection's attributes for _BaudRate_, _ReceiveFifoDepth_, _Timeout_,
_Parity_, _DataBits_, and _StopBits_. The caller passes in 0 for any of these
values that should be set to the default value. _Parity_ and _StopBits_ are
enumerated values with their default value set in the 0th. If any of the
parameters is an invalid value then the function returns
`EFI_INVALID_PARAMETER`; the only other valid fail return value is
`EFI_DEVICE_ERROR` if the serial device is physically not functioning correctly.

The _Mode_ pointer must be updated in this function when success has been
determined, but not modified if there is an error.

If any attribute is modified that changes any field of the UART Device Path
Node for this device, then the Device Path Protocol must be reinstalled with
the UEFI Boot Service `ReinstallProtocolInterface()`.
