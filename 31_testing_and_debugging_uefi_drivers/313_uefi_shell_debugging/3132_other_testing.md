<!--- @file
  31.3.2 Other Testing

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

### 31.3.2 Other Testing

There are other tests that can be performed from within the UEFI shell. These
are not testing a specific protocol, but are testing functionality and for
other coding practices.

<div style="page-break-after: always;"></div>

###### Table 41-Other Shell Testing Procedures

| **Shell Command Sequence** | **What it tests** |
| -------------------------- | ----------------- |
|`Shell> Memmap` <br/> `Shell> Dh` <br/> `Shell> Load DriverName.efi` <br/> `Shell> Memmap` <br/> `Shell> Dh` <br/> `Shell> Unload DriverHandle` <br/> `Shell> Memmap` <br/> `Shell> Dh`  | Tests for incorrectly matched up DriverEntryPoint and Unload() functions. This catches memory allocation that is not unallocated, and catches protocols that are installed and not uninstalled, etc. |
|`Shell> Memmap` <br/> `Shell> Connect DeviceHandle DriverHandle` <br/> `Shell> Memmap` <br/> `Shell> Disconnect DeviceHandle DriverHandle` <br/> `Shell> Memmap` <br/> `Shell> Reconnect DeviceHandle` <br/> `Shell> Memmap` | Tests for incorrectly matched up Driver Binding Start() and Stop() functions. This catches memory allocation that is not unallocated. |
|`Shell> dh` <br/> `Shell> Connect DeviceHandle DriverHandle` <br/> `Shell> dh` <br/> `Shell> Disconnect DeviceHandle DriverHandle` <br/> `Shell> dh` <br/> `Shell> Reconnect DeviceHandle` <br/> `Shell> dh` | Tests for incorrectly matched up Driver Binding Start() and Stop() functions. This catches protocols that are installed and not uninstalled. |
|`Shell> OpenInfo DeviceHandle` <br/> `Shell> Connect DeviceHandle DriverHandle` <br/> `Shell> OpenInfo DeviceHandle` <br/> `Shell> Disconnect DeviceHandle DriverHandle` <br/> `Shell> OpenInfo DeviceHandle` <br/> `Shell> Reconnect DeviceHandle` <br/> `Shell> OpenInfo DeviceHandle` | Tests for incorrectly matched up Driver Binding Start() and Stop() functions. This catches protocols that are opened and not closed. |
