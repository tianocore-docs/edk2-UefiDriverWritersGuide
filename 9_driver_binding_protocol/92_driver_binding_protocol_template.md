<!--- @file
  9.2 Driver Binding Protocol Template

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

## 9.2 Driver Binding Protocol Template

The implementation of the Driver Binding Protocol for a specific driver is
typically found in the file `<<DriverName>>`.c. This file contains the instance
of the `EFI_DRIVER_BINDING_PROTOCOL` along with the implementation of the
`Supported()`, `Start()`, and `Stop()` services. _Appendix A_ contains the
template for a UEFI Driver and includes the declaration of the Driver Binding
Protocol instance, the Driver Binding Protocol services and the driver entry
point that uses the EDK II library `UefiLib` functions to install the Driver
Binding Protocol into the handle database and complete the initialization of
the Driver Binding Protocol data fields.

The `Supported()`, `Start()`, and `Stop()` services are covered in detail in
the EFI Driver Binding Protocol section of the _UEFI Specification_. Also included are code
examples and the detailed algorithms to implement these services for device
drivers and bus drivers If a UEFI Driver produces multiple instances of the
Driver Binding Protocol, they are all installed in the driver entry point.
Each instance of the Driver Binding Protocol is implemented using the same guidelines.
The different instances may share worker functions to reduce the size of the driver.

The `Supported()` service performs a quick check to see if a driver supports a
controller. The `Supported()` service `must` `not` modify the state of the
controller because the controller may already be managed by a different driver.
If the `Supported()` service passes, the `Start()` service is called to ask the
driver to bind to a specific controller. The `Stop()` service does the opposite
of `Start()`. It disconnects a driver from a controller and frees any resources
allocated in the `Start()` services.

**********
**TIP:**  Although the thought of initializing something as soon as it is supported in the `Supported()` service of the driver seems to make sense, the `Supported()` service is intended only to be a quick check to find out if a driver can make a connection to the specified controller, find out if it has already been called (started and in use), or if it is in use exclusively by another component. The `Supported()` service must return an error if the controller is already in use or is in use exclusively by another component.
**********

Initializing or modifying tasks should only be done in the `Start()` service of the driver, not in the `Supported()` service.

**********
**Tip**: This guide provides additional recommendations for implementing the  Driver Binding Protocol for devices on industry standard busses such as PCI, USB, SCSI, and SATA. Please see the chapter on the specific bus type for additional details.                                                                                      -----
**********

None of the Driver Binding Protocol services are allowed to use the console I/O
protocols. A UEFI Driver may use the `DEBUG()` and `ASSERT()` macros from the
EDK II library `DebugLib` to send messages to the standard error console if it
is active. These macros are usually enabled during UEFI Driver development and
are disabled when a UEFI Driver is released.
