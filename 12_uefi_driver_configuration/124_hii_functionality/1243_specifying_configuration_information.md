<!--- @file
  12.4.3 Specifying configuration information

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

### 12.4.3 Specifying configuration information

HII functionality makes it easier to publish configuration information to a
database. With HII functionality, the driver writer specifies the form layout
for configuration information. The form layout points to static strings, as
well as to data that is configurable by the user. The driver writer also
defines the data structure of configurable data stored in NVRAM.

The strings are defined in a Unicode file (files with a .uni extension). During
the driver's init section, the driver publishes the list of strings (such as
language strings) and forms to the HII database with the HII handler. The
driver also publishes its configuration routing protocols. The actual data
structure of strings and forms is created as part of the build process.

The build tools take the .Uni file and the .Vfr file and produce a data
structure. That data structure is stored in the HII database. Configurable data
is stored in NVRAM.

When the HII engine is invoked, it runs the forms, pulls the strings it needs
from the string database, and pulls the configurable settings it needs from
NVRAM.

#### 12.4.3.1 Using forms

Prior to HII, there was no standardized way to create forms. Instead, forms
were created manually, and were manually output to the console. HII provides a
standard way to create forms, making it easier to display information. Because
HII functionality is standardized via forms, the driver no longer needs to
manage the way users enter data, or worry about parsing the data. The HII
engine parses the data to make sure it is appropriate for the defined field.
See the discussion earlier in this section entitled "General Steps for
Implementing HII Functionality."

To create forms, a UEFI Driver with HII functionality should use the VFR
programming language and IFR defined in the Human Interface Infrastructure
Overview chapter of the _UEFI Specification_. Refer to the _VFR Programming
Language_ for information about creating forms. The `MdeModulePkg` also contains a sample driver in the paths `MdeModulePkg/Universal/DriverSampleDxe` and `MdeModulePkg/Universal/HiiResourcesSampleDxe` that show example usages of VFR constructs.

#### 12.4.3.2 Storing configuration information in nonvolatile storage

A UEFI Driver should store its configurable information in nonvolatile storage
(NVRAM). This configuration information should be stored with the device so the
configuration information travels with the device if it is moved between
platforms.

The exact method for retrieving and storing configuration information on a
device is device specific. Typically, drivers use the services of a bus I/O
protocol to access the resources of a device to retrieve and store
configuration information. For example, if a PCI controller has a flash device
attached to it, the management of that flash device may be exposed through I/O
or memory-mapped I/O registers described in the BARs associated with the PCI
device. A PCI device driver can use the `Io.Read()`, `Io.Write()`,
`Mem.Read()`, or `Mem.Write()` services of the PCI I/O Protocol to access the
flash contents to retrieve and store configuration settings. Devices that are
integrated onto the motherboard or are part of a FRU may use the UEFI variable
Services such as `GetVariable()` and `SetVariable()` to store configuration
information.
