<!--- @file
  14.3 Repair() Implementation

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

## 14.3 Repair() Implementation

The `Repair()` service attempts repair operations on a driver-managed
controller or a child the driver has produced. This service is not allowed to
use any of the console-I/Orelated protocols. Instead, the status of the repair
operation is returned to the caller. The `Repair()` service supports an
optional parameter called _ProgressNotification_ that may be `NULL`. The caller
may pass in a notification function to `Repair()` so the caller can inform the
user of the progress during extended repair operations. If a repair operation
takes a short period of time to execute, _ProgressNotification_ may be ignored.
If the repair operation takes an extended period of time to execute, the UEFI
Driver should periodically call the function specified by
_ProgressNotification_ with _Value_ and _Limit_ parameters expressing the
amount of repair work currently completed. The caller may choose to log or
display the progress of the repair operation as well as the final results of
the repair operation.
