<!--- @file
  3.14.2 Loading UEFI option ROM drivers

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

### 3.14.2 Loading UEFI option ROM drivers

The following is an interesting use case that tests these precedence rules.
Assume that the following three identical adapters are in the system:

* Adapter A: UEFI driver Version 0x10

* Adapter B: UEFI driver Version 0x11

* Adapter C: No UEFI driver

These three adapters have UEFI drivers in the option ROM as defined below. When
UEFI drivers connect, the drivers control the devices as follows:

* UEFI driver Version 0x10 manages Adapter A.

* UEFI driver Version 0x11 manages Adapter B and Adapter C.

If the UEFI driver version 0x12 is soft loaded through the UEFI Shell, nothing
changes until the existing drivers are disconnected and a reconnect is
performed. This reconnection can be done in a variety of ways but the UEFI
Shell command `reconnect r` is the easiest.

The drivers now control the devices as follows:

* UEFI driver Version 0x10 manages Adapter A.

* UEFI driver Version 0x11 manages Adapter B. 

* UEFI driver Version 0x12 manages Adapter C.

An IHV can override this logic by implementing the Driver Family Override
Protocol.

An OEM can override this logic by implementing the Platform Driver Override
Protocol.
