<!--- @file
  3.15.9 Boot Manager Connect All Processing

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

### 3.15.9 Boot Manager Connect All Processing

On some platforms, the boot manager may connect all drivers to all devices at
this point in the platform initialization sequence. However, platform firmware
can choose to connect the minimum number of drivers and devices that is
required to establish consoles and gain access to the boot device. Performing
the minimum amount of work is recommended to enable shorter boot times.

If the platform firmware chooses to go into a "platform configuration" mode,
then all the drivers should be connected to all devices. The platform follows
the following sequence:

1. A search is made of the handle database for all root controller handles.
   These handles do not have a Driver Binding Protocol or the Loaded Image
   Protocol. They have a Device Path Protocol, and no parent controllers.
2. `ConnectController()` is called with the _Recursive_ flag set to `TRUE` and
   a _RemainingDevicePath_ of `NULL` for each of the root controllers. These
   settings cause all children to be produced by all bus drivers.
3. As each bus is expanded, and if the bus supports storage devices for UEFI
   drivers, additional UEFI drivers are then loaded from those storage devices
   (for example, option ROMs on PCI adapters).
4. This process is recursive. Each time a child handle is created, `ConnectController()` is called again on that child handle, so all of those handle's children are produced.
5. When the process is complete, the entire tree of boot devices in the system
   hierarchy is present in the handle database.
