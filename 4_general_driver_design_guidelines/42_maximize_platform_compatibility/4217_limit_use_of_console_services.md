<!--- @file
  4.2.17 Limit use of Console Services

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

### 4.2.17 Limit use of Console Services

PC BIOS legacy option ROMs typically display banners and allow hotkey(s) to
enter the configuration area for a particular card. Current UEFI drivers use
HII functionality to allow access to system configuration areas.

Because UEFI drivers now have HII functionality, the UEFI Driver Model requires
that no console I/O operations take place in the UEFI Driver Binding Protocol
functions. A reasonable exception to this rule is to use the `DEBUG()` macro to
display progress information during driver development and debug. Using the
`DEBUG()` macro allows the code for displaying the data to be easily removed
for a production build of the driver.

Use of the `DEBUG()` macro should be limited to "debug releases" of a driver.
This strategy typically works if the driver is loaded after the UEFI console is
connected. However, because console drivers may live in option ROMs, some
firmware implementations may load the option ROM drivers before the UEFI
console is connected. In such cases, the _ConOut_ and _StdErr_ fields of the
UEFI system table may be `NULL`, and printing can crash the system. The
`DEBUG()` macro should check to see if the field is `NULL` before using those
services.
