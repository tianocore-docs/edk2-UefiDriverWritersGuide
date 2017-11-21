<!--- @file
  31.3.7 Testing the Driver Configuration Protocol

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

### 31.3.7 Testing the Driver Configuration Protocol

The DrvCfg command may be used to list all devices that are being managed by
UEFI drivers that support the Driver Configuration Protocols. The `Devices` and
`Drivers` commands show the drivers that support the Driver Configuration
Protocol and the devices that those drivers are managing or have produced. Once
a device is selected, the `DrvCfg` command may be used to invoke the
`SetOptions()`, `ForceDefaults()`, or `OptionsValid()` services of the Driver
Configuration Protocol. The code below shows examples of using the `DrvCfg`
command:

* Example 1: Displays all the devices that are being managed by UEFI drivers that
support the obsolete Driver Configuration Protocol.

  `fs0:> DrvCfg`

* Example 2: Forces defaults on all the devices in the system.

  `fs0:> DrvCfg -f`

* Example 3: Validates the options on all the devices in the
system.

  `fs0:> DrvCfg -v`

* Example 4: Invokes the `SetOptions()` service of the Driver Configuration
Protocol for the driver on handle 23 and its child process (27).

  `fs0:> DrvCfg -s 23 27`
