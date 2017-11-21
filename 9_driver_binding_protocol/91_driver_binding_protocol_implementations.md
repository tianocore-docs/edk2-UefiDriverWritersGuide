<!--- @file
  9.1 Driver Binding Protocol Implementations

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

## 9.1 Driver Binding Protocol Implementations

The implementation of the Driver Binding Protocol for a specific driver is
typically found in the file `<<DriverName>>`.c. Appendix A contains a template
for a `<<DriverName>>`.c file for a UEFI Driver. This file typically performs
and contains the following:

* Adds a global variable for the `EFI_DRIVER_BINDING_PROTOCOL` instance to
  `<<DriverName>>.c`.

* An implementation of the `Supported()` service

* An implementation of the `Start()` service

* An implementation of the `Stop()` service

* Installs all the Driver Binding Protocols in the driver entry point

* If the UEFI Driver supports the unload feature, it then uninstalls all the
  Driver Binding Protocols in the `Unload()` function.

The example below shows the protocol interface structure for the Driver Binding
Protocol for reference. It is composed of the three services called
`Supported()`, `Start()`, and `Stop()`, along with the three data fields called
`Version`, `ImageHandle`, and `DriverBindingHandle`.

###### Example 122-Driver Binding Protocol

```c
typedef struct _EFI_DRIVER_BINDING_PROTOCOL EFI_DRIVER_BINDING_PROTOCOL;

///
/// This protocol provides the services required to determine if a driver
/// supports a given controller. If a controller is supported, then it
/// also provides routines to start and stop the controller.
///
struct _EFI_DRIVER_BINDING_PROTOCOL {
  EFI_DRIVER_BINDING_SUPPORTED Supported;
  EFI_DRIVER_BINDING_START Start;
  EFI_DRIVER_BINDING_STOP Stop;
  
  ///
  /// The version number of the UEFI driver that produced the
  /// EFI_DRIVER_BINDING_PROTOCOL. This field is used by
  /// the EFI boot service ConnectController() to determine
  /// the order that driver's Supported() service will be used when
  /// a controller needs to be started. EFI Driver Binding Protocol
  /// instances with higher Version values will be used before ones
  /// with lower Version values. The Version values of 0x0-
  /// 0x0f and 0xfffffff0-0xffffffff are reserved for
  /// platform/OEM specific drivers. The Version values of 0x10-
  /// 0xffffffef are reserved for IHV-developed drivers.
  ///
  UINT32 Version;
  
  ///
  /// The image handle of the UEFI driver that produced this instance
  /// of the EFI_DRIVER_BINDING_PROTOCOL.
  ///
  EFI_HANDLE ImageHandle;
  
  ///
  /// The handle on which this instance of the
  /// EFI_DRIVER_BINDING_PROTOCOL is installed. In most
  /// cases, this is the same handle as ImageHandle. However, for
  /// UEFI drivers that produce more than one instance of the
  /// EFI_DRIVER_BINDING_PROTOCOL, this value may not be
  /// the same as ImageHandle.
  ///
  EFI_HANDLE DriverBindingHandle;
};
```

UEFI Drivers declare a global variables for the Driver Binding Protocol
instances produced. The _ImageHandle_ and _DriverBindingHandle_ fields are
pre-initialized to `NULL`. A UEFI Driver can initialize the _ImageHandle_ and
_DriverBindingHandle_ fields in the driver entry point, or use the EDK II
library `UefiLib` functions to help initialize UEFI Drivers that fill and
initialize the _ImageHandle_ and _DriverBindingHandle_ fields automatically.
The _Version_ field must be initialized by the UEFI Driver. Higher _Version_
values signify a newer driver. This field is a 32-bit value, but the values
0x0-0x0F and 0xFFFFFFF0- 0xFFFFFFFF are reserved for UEFI drivers written by
OEMs. IHVs may use the values 0x10-0xFFFFFFEF. Each time a new version of a UEFI driver is released, the _Version_ field must be increased. The following example shows how a Driver
Binding Protocol is typically declared in a driver.

###### Example 123-Driver Binding Protocol declaration

```c
#include <Uefi.h>
#include <Protocol/DriverBinding.h>

EFI_DRIVER_BINDING_PROTOCOL gAbcDriverBinding = {
  AbcSupported,          // Supported()
  AbcStart,              // Start()
  AbcStop,               // Stop()
  0x10,                  // Version
  NULL,                  // ImageHandle
  NULL                   // DriverBindingHandle };
```

The implementations of the Driver Binding Protocol change in complexity
depending on the driver type. A device driver is the simplest to implement. A
bus driver or a hybrid driver may be more complex because it has to manage both
the bus controller and child controllers.

The `EFI_DRIVER_BINDING_PROTOCOL` is installed onto the driver's image handle.
It is possible for a driver to produce more than one instance of the Driver
Binding Protocol. All additional instances of the Driver Binding Protocol must
be installed onto new handles.

The Driver Binding Protocol can be installed directly using the UEFI Boot
Service `InstallMultipleProtocolInterfaces()`. However, the EDK II library
`UefiLib` also provides a number of helper functions to install the Driver
Binding Protocol and the optional UEFI Driver Model related protocols. The
following helper functions are covered in more detail in _Chapter 7_:
* `EfiLibInstallDriverBinding()`
* `EfiLibInstallAllDriverProtocols()`
* `EfiLibInstallDriverBindingComponentName2()`
* `EfiLibInstallAllDriverProtocols2()`

If an error is generated when installing any of the Driver Binding Protocol
instances, the entire driver should fail and return a error status such as
`EFI_ABORTED`. If a UEFI Driver implements the `Unload()` feature, any Driver
Binding Protocol instances installed in the driver entry point must be
uninstalled in the `Unload()` function.
