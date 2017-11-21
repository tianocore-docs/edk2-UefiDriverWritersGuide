<!--- @file
  3.15.10 Boot Manager Driver List Processing

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

### 3.15.10 Boot Manager Driver List Processing

The platform boot manager loads the drivers that are specified by the
_DriverOrder_ and _Driver####_ environment variables. These environment
variables are discussed in more detail in the Boot Manager chapter of the _UEFI
Specification_.

Before the platform boot manager loads each driver, it uses the device path
stored in the _Driver####_ variable to connect the controllers and drivers that
are required to access the driver option. This process is exactly the same as
the process used for the console variables _ErrOut_, _ConOut_, and _ConIn_.

If any driver in the _DriverOrder_ list has a load attribute of
`LOAD_OPTION_FORCE_RECONNECT`, then the platform boot manager uses the
`DisconnectController()` and

`ConnectController()` boot services to disconnect and reconnect all the drivers
in the platform. This load attribute allows the newly loaded drivers to be
considered in the driver connection process.

For example, if no driver in the _DriverOrder_ list has the
`LOAD_OPTION_FORCE_RECONNECT` load attribute, then it would be possible for a
built-in system driver with a lower version number to manage a device. Then,
after loading a newer driver with a higher version number from the
_DriverOrder_ list, the driver with the lower version number is still managing
the same device.

However, if the newer driver in the _DriverOrder_ list has a load attribute of
`LOAD_OPTION_FORCE_RECONNECT`, then the platform boot manager disconnects and
reconnects all the controllers, so the driver with the highest version number
manages the same device that the lower versioned driver used to manage. Drivers
that are added to the _DriverOrder_ list should not set the
`LOAD_OPTION_FORCE_RECONNECT` attribute unless they have to because the
disconnect and reconnect process increases the boot time.
