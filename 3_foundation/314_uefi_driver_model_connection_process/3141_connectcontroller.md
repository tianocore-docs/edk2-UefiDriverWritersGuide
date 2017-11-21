<!--- @file
  3.14.1 ConnectController()

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

### 3.14.1 ConnectController()

By passing the handle of a specific controller into `ConnectController()`, UEFI
follows a specific process to determine which driver(s) manage the controller.
For reference, the following example is the definition of `ConnectController()`:

###### Example 6-ConnectController() UEFI Boot Service

```c
/**
  Connects one or more drivers to a controller.
  
  @param  ControllerHandle          The handle of the controller to which driver(s) are to be connected.
  
  @param  DriverImageHandle         A pointer to an ordered list handles that support
                                    The EFI_DRIVER_BINDING_PROTOCOL.
									
  @param  RemainingDevicePath       A pointer to the device path that specifies a child of the controller
                                    specified by ControllerHandle.
									
  @param  Recursive                 If TRUE, then ConnectController() is called recursively until the
                                    entire tree of controllers below the controller specified by
                                    ControllerHandle have been created. If FALSE, then the tree of
                                    controllers is only expanded one level.
									
  @retval  EFI_SUCCESS              1) One or more drivers were connected to
                                       ControllerHandle.
                                    2) No drivers were connected to ControllerHandle,
                                       But RemainingDevicePath is not NULL, and it is
                                       an End Device Path Node.
									   
  @retval  EFI_INVALID_PARAMETER    ControllerHandle is NULL.
  
  @retval  EFI_NOT_FOUND            1) There are no EFI_DRIVER_BINDING_PROTOCOL
                                       Instances present in the system.
                                    2) No drivers were connected to ControllerHandle.
**/

typedef
EFI_STATUS
(EFIAPI * EFI_CONNECT_CONTROLLER)(
  IN EFI_HANDLE                     ControllerHandle,
  IN EFI_HANDLE                     *DriverImageHandle, OPTIONAL
  IN EFI_DEVICE_PATH_PROTOCOL       *RemainingDevicePath, OPTIONAL
  IN BOOLEAN                        Recursive
  );
```

The connection is a two-phase process:

1. Construct an ordered list of driver handles from highest to lowest priority.

2. Attempt to connect the drivers to a controller in priority order from
   highest to lowest.

The following table lists the steps for phase one; _driver connection
precedence rules._ Much of this information is in the _UEFI Specification_
where the UEFI boot service `ConnectController()`is discussed.

<div style="page-break-after: always;"></div>

###### Table 12-Connecting controllers: Driver connection precedence rules

| **Step** | **Type of override**   | **Description**                                                            |
| -------- | ---------------------- | -------------------------------------------------------------------------- |
| 1        | Context override       | The parameter _DriverImageHandle_ is an ordered list of handles that support the `EFI_DRIVER_BINDING_PROTOCOL`. The highest priority image handle is the first element of the list, and the lowest priority image handle is the last element of the list. The list is terminated with a `NULL` image handle. <br/><br/> This parameter is usually `NULL` and is typically used only to debug new drivers from the UEFI Shell. These drivers are placed at the top of the ordered list of driver handles.   |
| 2      | Platform driver override | If an `EFI_PLATFORM_DRIVER_OVERRIDE_PROTOCOL` instance is present in the system, the `GetDriver()` service of this protocol is used to retrieve an ordered list of image handles for _ControllerHandle_. From this list, the image handles found in rule (1) above are removed. The first image handle returned from `GetDriver()` has the highest precedence, and the last image handle returned from `GetDriver()` has the lowest. The ordered list is terminated when `GetDriver()` returns `EFI_NOT_FOUND`. It is legal for no image handles to be returned by `GetDriver()`. There can be, at most, a single instance in the system of the `EFI_PLATFORM_DRIVER_OVERRIDE_PROTOCOL`. If there is more than one, then the system behavior is not deterministic. <br/><br/> The `EFI_PLATFORM_DRIVER_OVERRIDE_PROTOCOL` is optional and, if present, is provided with the platform firmware. This protocol is typically provided when a platform needs to guarantee that a specific UEFI Driver be used to manage a specific controller, which is typically only required for controllers that are integrated into the platform.    |
| 3      | Driver family override   | The list of available driver image handles can be found by using the boot service `LocateHandle()` with a _SearchType_ of `ByProtocol` for the GUID of the `EFI_DRIVER_FAMILY_OVERRIDE_PROTOCOL`. From this list, the image handles found in rules (1), and (2) above are removed. The remaining image handles are sorted from highest to lowest based on the value returned from the `GetVersion()` function of the `EFI_DRIVER_FAMILY_OVERRIDE_PROTOCOL` associated with each image handle.  <br/><br/>  The `EFI_DRIVER_FAMILY_OVERRIDE_PROTOCOL` is optional and is typically produced by UEFI Drivers associated with a family of controllers, When multiple versions of a UEFI Driver for a family of controllers are present in a platform, the UEFI Driver needs to determine which version of the UEFI Driver is best suited to manage a specific controller in the family of controllers.      |
| 4      | Bus                      | If there is an instance of the specific driver override `EFI_BUS_SPECIFIC_DRIVER_OVERRIDE_PROTOCOL` attached to _ControllerHandle_, then the `GetDriver()` service of this protocol is used to retrieve an ordered list of image handles for _ControllerHandle_. From this list, the image handles found in rules (1), (2), and (3) above are removed. The first image handle returned from `GetDriver()` has the highest precedence, and the last image handle returned from `GetDriver()` has the lowest precedence. The ordered list is terminated when `GetDriver()` returns `EFI_NOT_FOUND`. It is legal for no image handles to be returned by `GetDriver()`.  <br/><br/> In practice, this precedent option allows the UEFI drivers that are stored in a PCI Option ROM of a PCI adapter to manage that specific PCI adapter. even if drivers with higher versions are available from PCI Option ROMs on other PCI adapters. This rule exists to make sure that if a particular UEFI Driver on a PCI adapter only works with the hardware on that specific PCI adapter, then a UEFI Driver from a different PCI adapter is not to be used to manage it. If an IHV does not like this precedence rule, the Driver Family Override Protocol can be implemented to override this behavior.                                  |
| 5      | Driver binding search    | The list of available driver image handles can be found by using the boot service `LocateHandle()` with a _SearchType_ of `ByProtocol` for the GUID of the `EFI_DRIVER_BINDING_PROTOCOL`. From this list, the image handles found in rules (1), (2), (3), and (4) above are removed. The remaining image handles are sorted from highest to lowest based on the _Version_ field of the `EFI_DRIVER_BINDING_PROTOCOL` instance associated with each image handle. <br/><br/>  In practice, this sorting means that a PCI adapter, for example, that does not have a UEFI driver in its PCI Option ROM is managed by the driver with the highest _Version_ number.    |

Phase two of the connection process checks each driver in the ordered list to
see if it supports the controller. This check calls the `Supported()` service
of the driver's Driver Binding Protocol and passes in the _ControllerHandle_
and the _RemainingDevicePath_. If successful, the `Start()` service calls the
Driver Binding Protocol and passes in the _ControllerHandle_ and
_RemainingDevicePath_. Each driver in the list is given an opportunity to
connect, even if a prior driver connected successfully. However, if a driver
with higher priority had already connected and opened the parent I/O protocol
with exclusive access, the other drivers would not be able to connect if they
also require exclusive access to the parent I/O protocol.

Use this type of connection process because the order in which drivers are
installed into the handle database is not deterministic. Drivers can be
unloaded and reloaded later, which changes the order of the drivers in the
handle database.

These precedent rules assume that the relevant drivers to be considered are
loaded into memory. This case may not be true for all systems. Large systems,
for example, may limit "bootable" devices to a subset of the total number of
devices in the system.

The `ConnectController()` function can be called several times during the UEFI
initialization. Use it to connect consoles, devices required to load drivers
from the driver list, and to connect devices required for the boot options to
be processed by the boot manager.
