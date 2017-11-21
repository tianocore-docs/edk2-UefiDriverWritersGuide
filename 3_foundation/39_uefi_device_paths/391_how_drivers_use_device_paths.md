<!--- @file
  3.9.1 How drivers use device paths

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

### 3.9.1 How drivers use device paths

UEFI drivers that manage physical devices must be aware of device paths. When
possible, UEFI drivers treat device paths as data structures. In general, UEFI
Drivers are not required to parse or understand the beginning of the device
path. They usually only need to understand the device path node associated with
the specific controller the UEFI Driver is managing and, potentially, the
device path node associated with child controllers the UEFI Driver may generate
by appending a new device path node to the end of the device path from the
parent controller.

* Root bridge drivers are required only to produce the device paths for the
  root bridges, which typically contain only a single ACPI device path node.

* For a child device, bus drivers usually just append a single device path node
  to that of the parent device. The bus drivers should not parse the contents
  of the parent device path. Instead, a bus driver appends the one device path
  node that it is required to understand to the device path of the parent
  device.

For example, consider a SCSI Bus Driver that produces child handles for the
mass storage devices on a SCSI channel. This UEFI Driver builds a device path
for each mass storage device. The device path is constructed by appending a
SCSI device path node to the device path of the SCSI channel. The SCSI device
path node simply contains the Physical Unit Number (PUN) and Logical Unit
Number (LUN) of the SCSI mass storage device.

The mechanism described above allows the construction of device paths to be a
distributed process. The bus drivers at each level of the system hierarchy are
required only to understand the device path nodes for their child devices. Bus
drivers understand their local view of the device path, and a group of bus
drivers from each level of the system bus hierarchy work together to produce
complete device paths for the console and boot devices that are used to install
and boot operating systems.

There are a number of functions in the EFI Device Path Utilities Protocol
defined by the _UEFI Specification_ to help manage device paths. The `MdePkg`
in the EDK II also provides a Device Path Library with many useful functions
and macros to manage device paths.
