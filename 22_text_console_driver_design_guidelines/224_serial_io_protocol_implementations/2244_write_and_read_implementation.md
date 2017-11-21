<!--- @file
  22.4.4 Write() and Read() Implementation

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

### 22.4.4 Write() and Read() Implementation

The `Write()` and `Read()` functions are used to write bytes out to the serial
device or read in from the serial device. The only two parameters that are
passed are the number of bytes and then either the buffer to write out or a
buffer to read the bytes into. The amount of time that this can take is
determined by the timeout value in the _Mode_ structure (as set by
`SetAttributes()`).

Some serial devices support FIFOs. At the time the Write() service is called,
the FIFO could be full which means the entire FIFO may need to flush before any
new characters can be added to the FIFO. In this case, the time that a UEFI
Driver may be required to wait may be longer that the time specified by the
TimeOut value in the Mode structure. The caller is not aware of the FIFO depth,
so it is not correct to return an `EFI_TIMEOUT` error if the timeout is due to
a full FIFO. Instead, the UEFI Driver should detect the FIFO depth if possible
and wait to that number of character times.
