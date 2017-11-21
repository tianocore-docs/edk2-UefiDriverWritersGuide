<!--- @file
  6.2.2 Optional Bus Driver Features

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

### 6.2.2 Optional Bus Driver Features

The following lists features that a bus driver can optionally implement.
Optional recommended features are noted below.

* Install one or more instances of the `EFI_COMPONENT_NAME2_PROTOCOL` in the
  driver's entry point. Implementing this feature is `strongly` `recommended`.
  It allows a driver to provide human-readable names for the name of the driver
  and the controllers that the driver is managing.

* Register one or more HII packages in the driver's entry point. HII packages
  provide strings, fonts, and forms that allow users (such as IT
  administrators) to change the driver's configuration. HII packages are only
  required if a driver must provide the ability for a user to change
  configuration settings for a device.

* Install one or more instances of the `EFI_DRIVER_DIAGNOSTICS2_PROTOCOL` in
  the driver's entry point. If a driver needs to provide diagnostics for the
  controllers that it manages, this protocol is required.

* Provide an `EFI_LOADED_IMAGE_PROTOCOL.Unload()` service, so the driver can be
  dynamically unloaded. It is `recommended` that this feature be implemented
  during driver development, driver debug, and system integration. It is
  `strongly recommended` that this service remain in drivers for add-in
  adapters to help debug interaction issues during system integration.

* Parses the _`RemainingDevicePath`_ parameter that is passed into the

`Supported()` and `Start()` services of the Driver Binding Protocol if it is
not `NULL`. This is `strongly recommended` so a bus driver can start only the
one child specified by _`RemainingDevicePath`_. Implementing this feature may
significantly improve platform boot performance.

* Install an `EFI_DEVICE_PATH_PROTOCOL` on each child handle that is created.
  This is required only if the child handle represents a physical device. If
  child handle represents a virtual device, then an `EFI_DEVICE_PATH_PROTOCOL`
  is not required.

* Install one or more instances of the `EFI_DRIVER_FAMILY_OVERRIDE_PROTOCOL` in
  the driver's entry point. This protocol is required only if a higher priority
  rule for connecting drivers to a controller through the UEFI Boot Service
  `ConnectController()`is needed.

* Install one or more instances of the `EFI_DRIVER_HEALTH_PROTOCOL` in the
  driver's entry point. This protocol is required only for drivers that manage
  devices that can be in a bad state that is recoverable through either a
  repair operation or configuration operation. - Install one or more instances
  of the

`EFI_BUS_SPECIFIC_DRIVER_OVERRIDE_PROTOCOL` in the driver's entry point. This
protocol is required only with bus drivers for a bus type where the devices on
the bus can provide a container for more than one UEFI Driver. An example bus
type is PCI where PCI Option ROMs on PCI Adapters may contain more than one
UEFI Driver.

* Install an instance of the `EFI_DRIVER_SUPPORTED_EFI_VERSION_PROTOCOL` in the
  driver's entry point. This protocol is required for PCI controllers or other
  plug-in cards. Implementation of this feature is `recommended`.

* Create an Exit Boot Services event in the driver's entry point. This feature
  is required only if the driver is required to place the devices it manages in
  a specific state just before control is handed to an operating system.

* Creates a Set Virtual Address Map event in the driver's entry point. This
  feature is required only for a device driver that is a UEFI runtime driver.

#### 6.2.2.1 Compatibility with Older EFI/UEFI Specifications

The following lists the features a bus driver can optionally implement to
provide compatibility with older versions of the _EFI Specification_ and _UEFI
Specification_.

* Install one or more instances of the `EFI_COMPONENT_NAME_PROTOCOL` in the
  driver's entry point. Implementing this feature is `strongly recommended` for
  drivers that are required to be compatible with EFI 1.10 It allows a driver
  to provide human-readable names for the name of the driver and the
  controllers the driver manages. The EDK II libraries provide easy methods to
  produce both the Component Name Protocol and the Component Name 2 Protocol
  with very little additional overhead.

* Install one or more instances of the `EFI_DRIVER_CONFIGURATION_PROTOCOL` in
  the driver's entry point. If a driver must be compatible with EFI 1.10, and
  has any configurable options, this protocol is required.

* Install one or more instances of the `EFI_DRIVER_CONFIGURATION2_PROTOCOL` in
  the driver's entry point. If a driver must be compatible with UEFI 2.0 and
  has any configurable options, this protocol is required.

* Install one or more instances of the `EFI_DRIVER_DIAGNOSTICS_PROTOCOL` in the
  driver's entry point. If a driver must be compatible with EFI 1.10 and
  provide diagnostics for the controllers that the driver manages, this
  protocols is required.
