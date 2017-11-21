<!--- @file
  6.2.6 Bus drivers that produce one child in Start()

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

### 6.2.6 Bus drivers that produce one child in Start()

If the _RemainingDevicePath_ parameter passed into `Supported()` and `Start()`
is `NULL`, the bus driver must produce child handles for all children. If
_RemainingDevicePath_ is not `NULL`, the bus driver should parse
_RemainingDevicePath_ and attempt to produce only the one child device
described by _RemainingDevicePath_. If the driver does not recognize the device
path node in _RemainingDevicePath_, or if the device described by the device
path node does not match any of the children currently attached to the bus
controller, the `Supported()` and `Start()` services should fail. If the
_RemainingDevicePath_ is recognized, and the device path node does match a
child device that is attached to the bus controller, a child handle should be
created for that one child device.

**********
**Note:** _This step does not make sense for all bus types because some require
the entire bus to be enumerated to produce even a single child. In these cases,
the_ RemainingDevicePath _should be ignored._
**********

If a bus type has the ability to produce a child handle without enumerating the
entire bus, this ability should be implemented. Implementing this feature
provides faster boot times and is one of the major advantages of the UEFI
driver model.

The UEFI boot manager may pass the _RemainingDevicePath_ of the console device
and boot devices to `ConnectController()`, and `ConnectController()` then pass
this same _RemainingDevicePath_ into the `Supported()` and `Start()` services
of the Driver Binding Protocol. This design allows the minimum number of
drivers to be started to boot an operating system. The process can be repeated,
so one additional child handle can be produced in each call to `Start()`.

Also, a few child handles can be created from the first few calls to `Start()`
and then a _RemainingDevicePath_ of `NULL` may be passed in, which would require the rest
of the child handle to be produced. For example, most SCSI buses do not need to
be scanned to create a handle for a SCSI device when SCSI PUN and SCSI LUN are
known ahead of time. By starting only the single mass storage boot device, on
the OS required SCSI boot channel, scanning of all the other SCSI devices can
be eliminated.
