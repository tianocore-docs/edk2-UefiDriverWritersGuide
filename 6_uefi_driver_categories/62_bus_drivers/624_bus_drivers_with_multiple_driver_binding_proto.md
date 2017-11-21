<!--- @file
  6.2.4 Bus drivers with multiple driver binding protocols

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

### 6.2.4 Bus drivers with multiple driver binding protocols

The driver entry point of a bus driver is very similar to the driver entry
point of a device driver. The discussion in _Section 6.1.5_ applies equally
well to both bus drivers and device drivers. The differences between bus
drivers and device drivers are exposed in the implementations of the Driver
Binding Protocol. The following discussions describe the behaviors of the
`Start()` function of the Driver Binding Protocol for each type of bus driver.

An example bus driver in EDK II that produces multiple instances of the `EFI_DRIVER_BINDING_PROTOCOL`, is the console splitter driver in the
`MdeModulePkg/Universal/Console/ConSplitterDxe` subdirectory. This driver
multiplexes multiple console output and console input devices into a single
virtual console device. It produces instances of the Driver Binding Protocol
for the following:

* Console input device

* Console output devices

* Standard error device

* Simple pointer devices

* Absolute pointer devices

This driver is an example of a single feature that can be added or removed from
a platform by adding or removing a single component. It could have been
implemented as five different drivers, but there were many common functions
between the drivers, so it also saved code space to combine these five
functions.
