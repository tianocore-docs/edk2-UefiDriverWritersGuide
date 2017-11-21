<!--- @file
  31.3.8 Testing the Driver Diagnostics Protocols

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

### 31.3.8 Testing the Driver Diagnostics Protocols

The DrvDiag UEFI Shell command provides the ability to test all the services of
the two Driver Diagnostics Protocols that may be produced by a UEFI driver.
This command is able to show the devices that are being managed by UEFI drivers
that support the Driver Diagnostics Protocols. The `Devices` and `Drivers`
commands show the drivers that support the Driver Diagnostics Protocols and the
devices that those drivers are managing or have produced. Once a device has
been chosen, the `DrvDiag` command can be used to invoke the `RunDiagnostics()`
service of the Driver Diagnostics Protocols. The code below shows the following
examples of the `DrvDiag` command:

* Example 1: Displays all the devices that are being managed by UEFI drivers that
support the Driver Diagnostics Protocols.

  `fs0:> DrvDiag`

* Example 2: Invokes the `RunDiagnostics()` service of the Driver Diagnostics
Protocols in standard mode for the driver on handle 15 and the device on handle
19.

  `fs0:> DrvDiag -s 15 19`

* Example 3: Invokes the `RunDiagnostics()` service of the Driver Diagnostics
Protocols in manufacturing mode for the driver on handle 15 and the device on
handle 19.

  `fs0:> DrvDiag -m 15 19`
