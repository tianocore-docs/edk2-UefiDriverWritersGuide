<!--- @file
  3.10 UEFI driver model

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

## 3.10 UEFI driver model

The Overview and UEFI Driver Model chapters of the _UEFI Specification_ define
the UEFI driver model. Drivers that follow the UEFI driver model share the same
image characteristics as UEFI applications. However, the model allows UEFI more
control over drivers by separating their loading into memory from their
starting and stopping. The table below lists the series of UEFI driver
model-related protocols that are used to accomplish this separation.

###### Table 10-Protocols separating the loading and starting/stopping of drivers

| **Protocol**                          | **Description**                                                   |
| ------------------------------------- | ----------------------------------------------------------------- |
| Driver Binding Protocol               | Provides functions for starting and stopping the driver, as well as a function for determining if the driver can manage a particular controller. The UEFI driver binding model requires this protocol.                         |
| Service Binding Protocols             | Provides a mechanism that allows protocols to support more than one consumer. UEFI Drivers that are required to produce protocols that need to be available to more than one consumer produce both the Driver Binding Protocol and a Service Binding Protocol.   |
| Driver Supported EFI Version Protocol | Provides information on the version of the _UEFI Specification_ to which the UEFI Driver conforms. The version information follows the same format as the version field in the EFI System Table.           |
| Driver Family Override Protocol       | Provides a mechanism for a UEFI Driver to express UEFI Driver specific version information among a family of UEFI Drivers that are used by ConnectController() to select the best driver to manage a specific controller.              |
| Driver Health Protocol                | Provides services that allow a UEFI Driver to express messages associated with the health status of a controller, suggest repair operations, and request configuration changes required to place the controller in a usable state.     |
| HII Config Access Protocol            | Provides services to retrieve and save configuration data for a controller managed by a UEFI Driver. Also provides a service that allows a setup browser to inform a UEFI Driver when specific setup browser actions are performed.    |
| HII Packages                          | Allows a UEFI Driver to register strings, fonts, images, keyboard mappings, and setup forms related to the configuration operations required for UEFI Driver managed controllers.                                                                                                                                                                              |
| Component Name 2 Protocol             | Provides functions for retrieving a human-readable name of a driver and the controllers that a driver is managing using language codes defined by _RFC 4646_.       |
| Driver Diagnostics 2 Protocols        | Provides functions for executing diagnostic functions on driver managed devices using _RFC 4646_ defined language codes.    |
| Component Name Protocol               | Provides functions for retrieving a human-readable name of a driver and the controllers that a driver is managing using language codes defined by _ISO 639-2_. This protocol is only required by a UEFI Driver that must be compatible with platforms that support only UEFI 2.0 or EFI 1.10 This protocol has been replaced by the Component Name 2 Protocol. |
| Driver Diagnostics Protocols          | Provides functions for executing diagnostic functions on driver managed devices using language codes defined by _ISO 639-2_. This protocol is only required by a UEFI Driver specifically compatible with platforms supporting only UEFI 2.0 or EFI 1.10 This protocol has been replaced by the Driver Diagnostics 2 Protocol.    |
| Driver Configuration Protocol         | Provides functions that allow users to configure devices a driver is managing using language codes defined by _ISO 639-2_. It also provides services to place a device into a default configuration. This protocol is only required by a UEFI Driver specifically compatible with platforms supporting only UEFI 2.0 or EFI 1.10.  This protocol has been replaced with HII functionality.    |

The new protocols are registered on the driver's image handle. HII packages are
registered in the HII database. In the UEFI driver model, the main goal of the
driver's entry point is to install theses protocols, register HII packages, and
exit successfully.

At a later point in the system initialization, UEFI can use these protocol
functions to operate the driver. A more complex driver may produce more than
one instance of the `EFI_DRIVER_BINDING_PROTOCOL`. In this case, additional
instances of the Driver Binding Protocol are installed on new handles. These
new handles may also optionally support the additional protocols listed in
_Table 10_ above.

The UEFI driver model follows the organization of physical/electrical
architecture by defining three basic types of UEFI boot time drivers:

* Device drivers

* Bus drivers

* Hybrid drivers, which have characteristics of both a device driver and a bus
  driver

Device drivers and bus drivers are distinguished by the operations they perform
in the `Start()` and `Stop()` services of the Driver Binding Protocol. By
walking through the

process of connecting a driver to a device, the roles and relationships of the
bus drivers and device drivers become evident; the following sections discuss
these two driver types.
