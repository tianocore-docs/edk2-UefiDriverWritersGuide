<!--- @file
  3.14 UEFI Driver Model Connection Process

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

## 3.14 UEFI Driver Model Connection Process

All UEFI Drivers that adhere to the UEFI Driver Model follow the same basic
procedure. When the driver is loaded, it installs a Driver Binding Protocol on
the image handle from which it was loaded. It may also update a pointer to the
`Unload()` service of the Loaded Image Protocol and install the Component Name
2 Protocol and the Component Name Protocol, if needed, so its name is visible
to any operator. The UEFI Driver then exits from the entry point with a return
status of `EFI_SUCCESS`, leaving the UEFI Driver resident in system memory.

The Driver Binding Protocol provides a version number and the following three
services:

* `Supported()`

* `Start()`

* `Stop()`

The Driver Binding Protocol is available on the driver's image handle after the
entry point is exited. Later on when the system is "connecting" drivers to
devices, the driver's Driver Binding Protocol `Supported()` service is called.

The `Supported()` service is passed a controller handle. The `Supported()`
function quickly examines the controller handle to see if it represents a
device that the driver knows how to manage. If so, it returns `EFI_SUCCESS`.
The system then starts the driver by calling the driver's `Start()` service,
passing in the supported controller handle. The driver can later be
disconnected from a controller handle by calling the `Stop()` service.

A platform connects the devices in a platform with the drivers available in the
platform. This connection process appears complex at first, but as the process
continues, it becomes evident that the same basic procedure is used over and
over again to accomplish the complex task. This description does not go into
all the details of the connection process but explains enough that the role of
various drivers in the connection process can be understood. This knowledge is
fundamental to designing new UEFI Drivers.

The UEFI boot service `ConnectController()` demonstrates the flexibility of the
UEFI Driver Model. The UEFI Shell command `connect` directly exposes much of
the functionality of this boot service and provides a convenient way to explore
the flexibility and control offered by `ConnectController()`.
