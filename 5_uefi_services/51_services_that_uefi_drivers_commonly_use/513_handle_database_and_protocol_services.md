<!--- @file
  5.1.3 Handle Database and Protocol Services

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

### 5.1.3 Handle Database and Protocol Services

There are several UEFI Boot Services used to add, retrieve, and remove contents
from the Handle Database. Concepts of the Handle Database and Protocols are
introduced in _Section 3.4_. This section provides code examples for the UEFI
Boot Services commonly used by UEFI Drivers to manage the Handle Database and
include the following:

* `InstallMultipleProtocolInterfaces()`

* `UninstallMultipleProtocolInterfaces()`

* `LocateHandleBuffer()`

* `LocateProtocol()`

* `OpenProtocol()`

* `OpenProtocolInformation()`

* `CloseProtocol()`

#### 5.1.3.1 InstallMultipleProtocolInterfaces() and UninstallMultipleProtocolInterfaces()

These services are used to do the following:

* Create new handles in the Handle Database.

* Remove a handle from the Handle Database.

* Add protocols to an existing handle in the Handle Database.

* Remove protocols from an existing handle in the Handle Database.

Extra services to create a new handle in the Handle Database and remove an
existing handle from the Handle Database are not required. Instead, the first
protocol installed onto a handle automatically creates a new handle and adds
that handle to the Handle Database. The last protocol removed from an existing
handle automatically removes the handle from the Handle Database and destroys
the handle. This means it is not possible for a handle to be present in the
Handle Database with zero protocols installed.

Another important concept is that a single handle in the Handle Database is not
allowed to have more than one instance of the same Protocol installed onto that
handle. If a UEFI Driver is required to produce more than one instance of the
same protocol, then the Protocol instances must be installed on different
handles in the Handle Database.

UEFI Drivers tend to manage more than one protocol at a time. Because of this,
it is recommended that `InstallMultipleProtocolInterfaces()` and
`UninstallMultipleProtocolInterfaces()` be used instead of the
`InstallProtocolInterface()` and `UninstallProtocolInterface()`. This results
in source code that is easier to maintain and also tends to produce smaller
executables. In addition, `InstallMultipleProtocolInterfaces()` provides more
extensive error checking than `InstallProtocolInterface()`, which allows
developers to catch coding errors sooner, and results in higher quality UEFI
Driver implementations. The main difference is that
`InstallMultipleProtocolInterfaces()` guarantees that a duplicate Device Path Protocol 
is never be added to the Handle Database. _Section 3.9_ introduces the
concept of Device Paths and the requirement for them to be unique.

The `InstallMultipleProtocolInterfaces()` and `UninstallMultipleProtocolInterfaces()`
services support adding and removing more than one protocol at a time through the
use of a variable argument list. Protocols are represented by a pair of pointers
to a protocol GUID and a protocol interface. These services parse pairs of arguments
until a `NULL` pointer for the protocol GUID parameter is encountered.

**********
**Note:** _If any errors are generated when the protocols are being added to a
handle, any protocols added before the error is returned, are automatically
removed by InstallMultipleProtocolInterfaces(). This means the state of the
handle in the handle database is identical to the state prior to the call._
**********
**Note:** _If any errors are generated when the protocols are being removed
from a handle, any protocols removed before the error is returned, are
automatically added back by UninstallMultipleProtocolInterfaces(). This means
the state of the handle in the handle database is identical to the state prior
to the call._
**********
**TIP:** If unexpected errors are returned by these services, try converting a
single call for multiple protocols to a series of calls that process one
protocol at a time. This allows the specific protocol causing the error
condition to be identified. It should be rare for these services to return an
error condition. If an error condition is present it is likely due to a
duplicate GUID, a duplicate device path, or an invalid handle.
**********
**Note:** _When an attempt is made to remove a protocol interface from a handle
in the handle database, the UEFI core firmware checks to see if any other UEFI
drivers are currently using the services of the protocol to be removed. If UEFI
drivers are using that protocol interface, the UEFI core firmware attempts to
stop those UEFI drivers with a call to DisconnectController(). This is a quick,
legal, and safe way to shut down any protocols associated with this driver's
stack._
**********
**Caution:** A serious issue can occur when a user removes and then reattaches
a device on a bus that supports hot-plugging. Driver writers must consider this
when writing drivers for hot-plug devices.
**********

_The issue occurs when other controllers are also using one, or more, of a
driver's protocols. In these cases, the `UninstallMultipleProtocolInterfaces`
service fails._

_If the call to `DisconnectController()` fails, the UEFI core firmware then
calls `ConnectController()` to put the handle database back into the same state
that it was in prior to the original call to
`UninstallMultipleProtocolInterfaces()`. This call to `ConnectController()` has
the potential to cause issues upon re-entry in UEFI drivers that must be
handled in the UEFI driver. These issues could include lost or missed connected
pointer linkages resulting in lost data, confused operation, crashes and other
errors. See _Chapter 31_ in this guide for recommendations on how to test UEFI
drivers._

##### 5.1.3.1.1 Protocols that may be added at the driver entry point

The following protocols may also be added in the driver entry point with the
`InstallMultipleProtocolInterfaces()` service. Please see _Chapter 7_ for more
details on how to install these protocols in a driver entry point along with
the recommendations on when each of these protocols should be installed in a
driver entry point. Later chapters of this guide cover the implementation of
these protocols in more detail.

* Driver Binding Protocol

* Component Name Protocol

* Component Name 2 Protocol

* Driver Configuration Protocol

* Driver Configuration 2 Protocol

* Driver Diagnostics Protocol

* Driver Diagnostics 2 Protocol

* HII Config Access Protocol

* Driver Health Protocol

* Driver Family Override Protocol

* Driver Supported EFI Version Protocol

##### 5.1.3.1.2 Removing protocols when a driver is unloaded

If a UEFI driver is unloadable, then the protocols that were added in the
driver entry point must be removed in the driver's `Unload()` function using
`UninstallMultipleProtocolInterfaces()`.

**********
**TIP:** Although the `Unload()` function is optional, uninstalling the
protocols in the `Unload()` function of a driver is not optional. The install
and uninstall sections must mirror each other for the protocols used by the
driver.
**********
**TIP:** The `load` and `unload` UEFI Shell commands may be used to test
driver load and unload services for handles and protocols.
**********

##### 5.1.3.1.3 Code example

The following code fragment shows how `InstallMultipleProtocolInterfaces()` can
be used from the entry point of a UEFI Driver to install driver related
protocols. This example installs the Driver Binding Protocol, required for UEFI
Drivers that follow the UEFI Driver Model, along with the Component Name 2
Protocol which is optional for UEFI Drivers that follow the UEFI Driver Model.
Both protocols are installed onto the image handle passed into the entry point
of the UEFI Driver, and the call to `InstallMultipleProtocolInterfaces()` uses
GUID/Pointer pairs terminated by a `NULL` GUID value. Additional optional
protocols could be added to this one call to
`InstallMultipleProtocolInterfaces()` depending on a specific UEFI Driver
requirements and capabilities.

###### Example 28-Install protocols in UEFI Driver entry point.

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/ComponentName2.h>

EFI_DRIVER_BINDING_PROTOCOL gMyDriverBinding = {
  MySupported,
  MyStart,
  MyStop, 0x10,
  NULL,
  NULL
};

EFI_COMPONENT_NAME2_PROTOCOL gMyComponentName2 = {
  MyGetDriverName,
  MyGetControllerName,
  "en"
};

EFI_STATUS Status;
EFI_HANDLE ImageHandle;

//
// Install the Driver Binding Protocol and the Component Name 2 Protocol
// onto the image handle that is passed into the driver entry point
//
Status = gBS->InstallMultipleProtocolInterfaces (
                &ImageHandle,
                &gEfiDriverBindingProtocolGuid,
                &gMyDriverBinding,
                &gEfiComponentName2ProtocolGuid,
                &gMyComponentName2,
                NULL
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

The code fragment in _Example 29_ performs the same work as the example above,
but uses the EDK II `UefiLib` to install the UEFI Driver Model related
protocols. In this specific case, the Driver Binding Protocol, Component Name
Protocol, and Component Name 2 Protocols are all installed using the `UefiLib` function
`EfiLibInstallDriverBindingComponentName2()`. The Component Name Protocol and
Component Name 2 Protocol implementations use the same functions for their
protocol implementations, thereby reducing the size overhead for supporting
both name protocols.

The EDK II `UefiLib` provides 4 functions that may be used to initialize a UEFI
Driver that follows the UEFI Driver Model. The Component Name Protocols are
declared with `GLOBAL_REMOVE_IF_UNREFERENCED` that guarantees the protocol
structures are removed from the final binary UEFI Driver image if the EDK II
build is configured to not produce the Component Name Protocols. It does not
make sense to use that declaration style for the Driver Binding Protocol since
that protocol must always be produced by a UEFI Driver that follows the UEFI
Driver Model.

The EDK II library `UefiLib` uses several Platform Configuration Database (PCD)
feature flags to enable and disable the Component Name Protocols at build time.
_Chapter 30_ covers how to build UEFI Drivers in the EDK II and also covers
configuration of UEFI Drivers through PCD settings.

###### Example 29-Install protocols in UEFI Driver entry point using UefiLib.

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/UefiLib.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/ComponentName.h>

#define MY_VERSION 0x10

EFI_DRIVER_BINDING_PROTOCOL gMyDriverBinding = {
  MySupported, MyStart,
  MyStop,
  MY_VERSION,
  NULL,
  NULL
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME_PROTOCOL gMyComponentName = {
  (EFI_COMPONENT_NAME_GET_DRIVER_NAME)            MyGetDriverName,
  (EFI_COMPONENT_NAME_GET_CONTROLLER_NAME)        MyGetControllerName,
  "eng"
};

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_COMPONENT_NAME2_PROTOCOL gMyComponentName2 = {
  MyGetDriverName,
  MyGetControllerName,
  "en"
};

EFI_STATUS  Status;
EFI_HANDLE  ImageHandle;

//
// Install driver model protocol(s).
//
Status = EfiLibInstallDriverBindingComponentName2 (
            ImageHandle,
            SystemTable,
            &gMyDriverBinding,
            ImageHandle,
            &gMyComponentName
            & gMyComponentName2
            );
if (EFI_ERROR (Status)) {
  return Status;
}
```

The code fragment below shows how the protocols installed in the previous
example would be uninstalled in a UEFI Driver's `Unload()` function. A UEFI
Driver is not required to implement the `Unload()` capability, but if the
`Unload()` capability is implemented, it must undo the work performed in the
entry point of the UEFI Driver just like `InstallMultipleProtocolInterfaces()`.
`UninstallMultipleProtocolInterfaces()` allows multiple protocols to be
specified in a single call using a set of GUID/Pointer arguments terminated by
a `NULL` GUID value.

###### Example 30-Uninstall protocols in UEFI Driver Unload() function.

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/ComponentName2.h>
#include <Protocol/ComponentName.h>

EFI_STATUS Status;
EFI_HANDLE ImageHandle;

//
// Uninstall the Driver Binding Protocol and the Component Name Protocol
// from the handle that is passed into the Unload() function.
//
Status = gBS->UninstallMultipleProtocolInterfaces (
                ImageHandle,
                &gEfiDriverBindingProtocolGuid,
                &gMyDriverBinding,
                &gEfiComponentName2ProtocolGuid,
                &gMyComponentName2,
                &gEfiComponentNameProtocolGuid,
                &gMyComponentName,
                NULL
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

UEFI device drivers add protocols for I/O services to existing handles in the
handle database in their `Start()` function and remove those same protocols
from those same handles in their `Stop()` function.

UEFI bus drivers may add protocols to existing handles, but they are also
responsible for creating handles for the child device on that bus. This
responsibility means that the UEFI bus driver typically adds the
`EFI_DEVICE_PATH_PROTOCOL` and an I/O abstraction for the bus type managed by
that bus driver. For example, the PCI bus driver creates child handles with
both the `EFI_DEVICE_PATH_PROTOCOL` and the `EFI_PCI_IO_PROTOCOL`. The bus
driver may also optionally add the `EFI_BUS_SPECIFIC_DRIVER_OVERRIDE_PROTOCOL`
to the child handles if the bus type supports a standard container for storing
UEFI Drivers.

The following code fragment shows an example of a how a child handle can be
added to the handle database with a Device Path Protocol and then add a Block
I/O Protocol to that same child handle. These two operations could also be
combined into a single call to `InstallMultipleProtocolInterfaces()`.

###### Example 31-Add child handle to handle database

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Protocol/DevicePath.h>
#include <Protocol/BlockIo.h>

EFI_STATUS                Status;
EFI_HANDLE                ChildHandle;
EFI_DEVICE_PATH_PROTOCOL  *DevicePath;
EFI_BLOCK_IO_PROTOCOL     *BlockIo;

//
// Add Device Path Protocol to a new handle
//
ChildHandle = NULL;
Status = gBS->InstallMultipleProtocolInterfaces (
                &ChildHandle,
                &gEfiDevicePathProtocolGuid,
                DevicePath,
                NULL
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Add the Block I/O Protocol to the handle created in the previous call
//
Status = gBS->InstallMultipleProtocolInterfaces (
                &ChildHandle,
                &gEfiBlockIoProtocolGuid,
                BlockIo,
                NULL
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

The following _code fragment below_ shows an example of a how the child handle
created in the previous example can be destroyed by uninstalling all the
installed protocols in a single call to `UninstallMultipleProtocolInterfaces()`.

###### Example 32-Remove child handle from handle database.

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Protocol/DevicePath.h>
#include <Protocol/BlockIo.h>

EFI_STATUS Status;
EFI_HANDLE ChildHandle;
EFI_DEVICE_PATH_PROTOCOL   *DevicePath;
EFI_BLOCK_IO_PROTOCOL      *BlockIo;

//
// Remove Device Path Protocol and Block I/O Protocol from the child
// handle created above. Because this call removes all the
// protocols from the handle, the handle is removed from the
// handle database.
//
Status = gBS->UninstallMultipleProtocolInterfaces (
                ChildHandle,
                &gEfiDevicePathProtocolGuid,
                DevicePath,
                &gEfiBlockIoProtocolGuid,
                BlockIo,
                NULL
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

A more rare use case of `InstallMultipleProtocolInterfaces()` is installing a
protocol with a `NULL` protocol interface pointer. The GUID value in this case
is called a tag GUID because there are no data fields or services associated
with the GUID.

The code fragment below shows an example of adding a tag GUID to the handle of
a controller that a UEFI Driver is managing. In this example, the tag GUID used
is the GUID name of the UEFI Driver itself called `gEfiCallerIdGuid`.

###### Example 33-Add tag GUID to a controller handle.

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS      Status;
EFI_HANDLE      ControllerHandle;

//
// Add tag GUID called gEfiCallerIdGuid to ControllerHandle
//
Status = gBS->InstallMultipleProtocolInterfaces (
                &ControllerHandle,
                &gEfiCallerIdGuid,
                NULL,
                NULL
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

The following code fragment shows how the tag GUID added in the previous
example can be removed.

###### Example 34-Remove tag GUID from a controller handle.

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS      Status;
EFI_HANDLE      ControllerHandle;

//
// Remove tag GUID called gEfiCallerIdGuid from ControllerHandle
//
Status = gBS->UninstallMultipleProtocolInterfaces (
                ControllerHandle,
                &gEfiCallerIdGuid,
                NULL,
                NULL
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

#### 5.1.3.2 LocateHandleBuffer()

This service retrieves a list of handles that meet a search criterion from the
handle database. The following are the search options:

* Retrieve `AllHandles`: Retrieve all handles in the handle database.

* Retrieve `ByProtocol`: Retrieve all handles in the handle database that
  support a specified protocol.

* Retrieve `ByRegisterNotify`: Retrieve the handle for which a specific protocol was just installed and configured for register notification using `RegisterProtocolNotify()`. This search option is strongly discouraged for UEFI Drivers. It was used with previous releases of the _EFI Specification_ before the introduction of the _UEFI_ Driver Model.

The buffer returned by `LocateHandleBuffer()` is allocated by the service
`AllocatePool()`. A UEFI driver using this service is responsible for freeing the returned buffer when the UEFI driver no longer requires its contents use the service `FreePool()`. The following code fragment shows how all the handles in the handle database can be retrieved.

###### Example 35-Retrieve all handles in handle database

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/MemoryAllocationLib.h>

EFI_STATUS      Status;
UINTN           HandleCount;
EFI_HANDLE      *HandleBuffer;

//
// Retrieve the list of all the handles in the handle database. The
// number of handles in the handle database is returned in HandleCount,
// and the array of handle values is returned in HandleBuffer which
// is allocated using AllocatePool().
//
Status = gBS->LocateHandleBuffer (
                AllHandles,
                NULL,
                NULL,
                &HandleCount,
                &HandleBuffer
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Free the array of handles allocated by gBS >LocateHandleBuffer()
//
FreePool (HandleBuffer);
```

The code fragment below shows how all the handles that support the Block I/O
Protocol can be retrieved and how the individual Block I/O Protocol instances
can be retrieved using `OpenProtocol()`.

###### Example 36-Retrieve all Block I/O Protocols in handle database

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/MemoryAllocationLib.h>
#include <Protocol/BlockIo.h>

EFI_STATUS             Status;
UINTN                  HandleCount;
EFI_HANDLE             *HandleBuffer;
UINTN Index;
EFI_BLOCK_IO_PROTOCOL  *BlockIo;

//
// Retrieve the list of handles that support the Block I/O
// Protocol from the handle database. The number of handles
// that support the Block I/O Protocol is returned in HandleCount,
// and the array of handle values is returned in HandleBuffer
// which is allocated using AlocatePool()
//
Status = gBS->LocateHandleBuffer (
                ByProtocol,
                &gEfiBlockIoProtocolGuid,
                NULL,
                &HandleCount,
                &HandleBuffer
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Loop through all the handles that support the Block I/O
// Protocol, and retrieve the Block I/O Protocol instance
// from each handle.
//
for (Index = 0; Index < HandleCount; Index++) {
  Status = gBS->OpenProtocol (
                  HandleBuffer[Index],
                  &gEfiBlockIoProtocolGuid,
                  (VOID **)&BlockIo,
                  gImageHandle,
                  NULL,
                  EFI_OPEN_PROTOCOL_GET_PROTOCOL
                  );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // BlockIo can be used here to make Block I/O Protocol
  // service requests.
  // 
 }
 
  //
  // Free the array of handles allocated by gBS->LocateHandleBuffer()
  //
  FreePool (HandleBuffer);
```

#### 5.1.3.3 LocateProtocol()

This service finds the first instance of a protocol interface in the handle
database. This service is typically used by UEFI drivers to retrieve service
protocols on service handles that are guaranteed to have, at most, one instance
of the protocol in the handle database. The _UEFI Specification_ defines the
following service protocols:

* `EFI_PLATFORM_DRIVER_OVERRIDE_PROTOCOL`

* `EFI_PLATFORM_TO_DRIVER_CONFIGURATION_PROTOCOL`

* `EFI_UNICODE_COLLATION_PROTOCOL`

* `EFI_DEBUG_SUPPORT_PROTOCOL`

* `EFI_DECOMPRESS_PROTOCOL`

* `EFI_ACPI_TABLE_PROTOCOL`

* `EFI_EBC_PROTOCOL`

* `EFI_BIS_PROTOCOL`

* `EFI_KEY_MANAGEMENT_SERVICE_PROTOCOL`

* `EFI_HII_FONT_PROTOCOL`

* `EFI_HII_STRING_PROTOCOL`

* `EFI_HII_IMAGE_PROTOCOL`

* `EFI_HII_DATABASE_PROTOCOL`

* `EFI_HII_CONFIG_ROUTING_PROTOCOL`

* `EFI_FORM_BROWSER2_PROTOCOL`

* `EFI_USER_MANAGER_PROTOCOL`

* `EFI_DEFERRED_IMAGE_LOAD_PROTOCOL` - `EFI_FIRMWARE_MANAGEMENT_PROTOCOL`

This service also supports retrieving protocols that have been notified with
`RegisterProtocolNotify()`, but use of `RegisterProtocolNotify()` is discouraged in UEFI
Drivers, so this use case of `LocateProtocol()` is not covered. See _Section
5.3.6_ for more details on `RegisterProtocolNotify()`.

The following code fragment shows how the `LocateProtocol()` service can be
used to retrieve the first instance of a service protocol in the handle
database. In this example the `EFI_DECOMPRESS_PROTOCOL` is used.

###### Example 37-Locate first Decompress Protocol in handle database

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Protocol/Decompress.h>
EFI_STATUS Status;
EFI_DECOMPRESS_PROTOCOL *Decompress;
Status = gBS->LocateProtocol (
                &gEfiDecompressProtocolGuid,
                NULL,
                (VOID **)&Decompress
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

#### 5.1.3.4 OpenProtocol() and CloseProtocol()

The `OpenProtocol()` and `CloseProtocol()` services are used by UEFI drivers to
acquire and release the protocol interfaces from the handle database that the
UEFI drivers require to produce their services. The `OpenProtocol()` service is
one of the most complex UEFI Boot Services because it is required to support
all of the various UEFI Driver types. UEFI applications and UEFI OS loaders may
also use these services to lookup and use protocol interfaces in the handle
database.

**********
**Caution:** Proper use of 'OpenProtocol()' and 'CloseProtocol()' is
required for interoperability with other UEFI components. There are UEFI Shell
commands that may be used to help verify the proper use of these services
including 'dh', 'connect', 'disconnect', 'reconnect', 'drivers',
'devices', 'devtree', and 'openinfo'.
**********

`OpenProtocol()` is typically used by the `Supported()` and `Start()` functions
of a UEFI driver to retrieve protocol interface(s) that are installed on
handles in the handle database. The code, below, shows the full function
prototype for the UEFI Boot Service `OpenProtocol()`

The `CloseProtocol()` service removes an element from the list of agents that
are consuming a protocol interface. UEFI drivers must close each protocol they
open when the UEFI Driver no longer requires the use of that protocol. Closing
protocols is typically done in the `Stop()` function.

###### Example 38-OpenProtocol() function prototype

```c
#define EFI_OPEN_PROTOCOL_BY_HANDLE_PROTOCOL   0x00000001
#define EFI_OPEN_PROTOCOL_GET_PROTOCOL         0x00000002
#define EFI_OPEN_PROTOCOL_TEST_PROTOCOL        0x00000004
#define EFI_OPEN_PROTOCOL_BY_CHILD_CONTROLLER  0x00000008
#define EFI_OPEN_PROTOCOL_BY_DRIVER            0x00000010
#define EFI_OPEN_PROTOCOL_EXCLUSIVE            0x00000020

/**
  Queries a handle to determine if it supports a specified protocol. 
  If the protocol is supported by the handle, it opens the protocol on behalf of the calling agent.
  @param  Handle                  The handle for the protocol interface that is being opened. 
  @param Protocol                 The published unique identifier of the protocol.
  @param  Interface               Supplies the address where a pointer to the corresponding Protocol Interface 
                                  is returned.
  @param  AgentHandle             The handle of the agent that is opening the protocol interface specified by 
                                  Protocol and Interface.
  @param  ControllerHandle        If the agent that is opening a protocol is a driver that follows
                                  the UEFI Driver Model, then this parameter is the controller 
								  handle that requires the protocol interface. If the agent does not 
								  follow the UEFI Driver Model, then this parameter is optional and 
								  may be NULL. @param Attributes The open mode of the protocol interface
								  specified by Handle and Protocol.
  @retval  EFI_SUCCESS            An item was added to the open list for the protocol interface, 
                                  and the protocol interface was returned in Interface. 
								  @retval EFI_UNSUPPORTED Handle does not support Protocol.
  @retval  EFI_INVALID_PARAMETER  One or more parameters are invalid.
  @retval  EFI_ACCESS_DENIED      Required attributes can't be supported in current environment.
  @retval  EFI_ALREADY_STARTED    Item on the open list already has required attributes whose 
                                  agent handle is the same as AgentHandle.
**/
typedef
EFI_STATUS
(EFIAPI * EFI_OPEN_PROTOCOL)(
  IN EFI_HANDLE  Handle,
  IN EFI_GUID                     *Protocol,
  OUT VOID                        **Interface, OPTIONAL
  IN EFI_HANDLE                   AgentHandle,
  IN EFI_HANDLE                   ControllerHandle,
  IN UINT32                       Attributes
  );
```

The `Handle` and `Protocol` parameters specify _what_ protocol interface is
being opened. The `AgentHandle` and `ControllerHandle` specifies _who_ is
opening the protocol interface.

The `Attributes` parameter specifies _why_ a protocol interface is being
opened. The `Interface` parameter is used to return the protocol interface if
it is successfully opened, and the `EFI_STATUS` return code tells if the
protocol interface was opened or not and if not, why it could not be opened.
The UEFI core records these input parameter values to track how each protocol
interface is being used. This tracking information can be retrieved through the
`OpenProtocolInformation()` service. The `EFI_STATUS` code returned by
`OpenProtocol()` is very important and must be examined by UEFI drivers that
use this service. In some cases, error code such as `EFI_ALREADY_STARTED` may
be the expected result and may not be an error at all for that specific UEFI
Driver.

**********
**Caution:** Make sure that all status codes returned by `OpenProtocol()` are
properly evaluated.
**********

_AgentHandle_ and _ControllerHandle_ describe "who" is opening the protocol
interface. For UEFI drivers, the _AgentHandle_ parameter is typically the
_DriverBindingHandle_ field from the `EFI_DRIVER_BINDING_PROTOCOL` produced by
the UEFI Driver. UEFI Drivers that are device drivers producing additional
protocols on the same handle typically use the same value for _Handle_ and
_ControllerHandle_. UEFI Drivers that are bus drivers producing child handles
may use `OpenProtocol()` with _Handle_ set to the handle for the bus controller
and _ControllerHandle_ set to the handle of a child controller.

The _Attributes_ parameter is a bitmask that describes "why" the protocol
interface is being opened. The `#define` values used to build an _Attributes_
value are also shown in _Example 38_ above. They are the `#define` statements.
A summary of the attribute combinations used by UEFI drivers is listed below.

**********
**Caution:** Make sure UEFI Drivers use the attributes correctly. If the
attributes are used incorrectly, a driver may not function properly and may
cause problems with other drivers. There are UEFI Shell commands to help verify
the proper use of attributes including **dh**, **connect**, **disconnect**,
**reconnect**, **drivers**, **devices**, **devtree**, and **openinfo**.
**EFI_OPEN_PROTOCOL_TEST_PROTOCOL**
**********

Tests to see if a protocol interface is present on a handle. Typically used in
the `Supported()` service of a UEFI driver if the services of the protocol
being tested are not required to complete the support check.

**EFI_OPEN_PROTOCOL_GET_PROTOCOL**

Retrieves a protocol interface from a handle. Typically used in the
`Supported()` and `Start()` services of a UEFI driver to make use of the
services of a protocol that is allowed to be used by more than one UEFI Driver.

**EFI_OPEN_PROTOCOL_BY_DRIVER**

Retrieves a protocol interface from a handle and marks that interface so it
cannot be opened by other UEFI drivers or UEFI applications unless the other
UEFI driver agrees to release the protocol interface. Typically used in the
`Supported()` and `Start()` services of a UEFI driver to use the services of a
protocol that is not allowed to be used by more than one UEFI Driver.

**EFI_OPEN_PROTOCOL_EXCLUSIVE**

Typically used by UEFI Applications to gain exclusive access to a protocol
interface.

If any drivers have the same protocol interface opened with an attribute of

`EFI_OPEN_PROTOCOL_BY_DRIVER`, then an attempt is made to remove them by
calling `Stop()` function in that UEFI Driver. If a UEFI Driver opens a
protocol interface with this attribute, no other drivers are allowed to open
the same protocol interface with the `EFI_OPEN_PROTOCOL_BY_DRIVER` attribute.
This attribute is used very rarely. `TIP:` For good coding practices, UEFI
Drivers that require the use of the `EFI_OPEN_PROTOCOL_EXCLUSIVE` attribute
should combine it with the `EFI_OPEN_PROTOCOL_BY_DRIVER` attribute.

`EFI_OPEN_PROTOCOL_BY_DRIVER | EFI_OPEN_PROTOCOL_EXCLUSIVE`

Retrieves a protocol interface from a handle and marks the interface so it
cannot be opened by other UEFI drivers or UEFI applications. This protocol is
not released until the driver that opened this attribute chooses to close it.
This attribute is used very rarely.

**EFI_OPEN_PROTOCOL_BY_CHILD_CONTROLLER**

Used by bus drivers. A bus driver is required to open the parent I/O
abstraction on behalf of each child controller that the bus driver produces.
This requirement allows the UEFI core to keep track of the parent/child
relationships no matter how complex the bus hierarchies become.

**EFI_OPEN_PROTOCOL_BY_HANDLE_PROTOCOL**

Do not use from a UEFI Driver. Only provided for backwards compatibility with
older versions of the _EFI Specification_. Use `EFI_OPEN_PROTOCOL_GET_PROTOCOL`
instead.

##### 5.1.3.4.1 Using EFI_OPEN_PROTOCOL_TEST_PROTOCOL

The code fragment below tests for the presence of the PCI I/O Protocol using the **EFI_OPEN_PROTOCOL_TEST_PROTOCOL** attribute. When this attribute is used, the protocol does not have to be closed because a protocol interface is not returned when this open mode is used.

###### Example 39-OpenProtocol() TEST_PROTOCOL

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/PciIo.h>

EFI_STATUS                                        Status;
EFI_DRIVER_BINDING_PROTOCOL                       *This;
EFI_HANDLE                                        ControllerHandle;

//
// Test to see if ControllerHandle supports the PCI I/O Protocol
//
Status = gBS->OpenProtocol (
                ControllerHandle,                 // Handle
                &gEfiPciIoProtocolGuid,           // Protocol
                NULL,                             // Interface
                This->DriverBindingHandle,        // AgentHandle
                ControllerHandle,                 // ControllerHandle
                EFI_OPEN_PROTOCOL_TEST_PROTOCOL   // Attributes
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

##### 5.1.3.4.2 Using EFI_OPEN_PROTOCOL_GET_PROTOCOL

The following code fragment shows the same example as above but retrieves the
PCI I/O Protocol using the `EFI_OPEN_PROTOCOL_GET_PROTOCOL` attribute. With
this attribute, the protocol does not have to be closed.

###### Example 40-OpenProtocol() GET_PROTOCOL

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/PciIo.h>

EFI_STATUS                                      Status;
EFI_DRIVER_BINDING_PROTOCOL                     *This;
EFI_HANDLE                                      ControllerHandle;

//
// Retrieve PCI I/O Protocol interface on ControllerHandle
//
Status = gBS->OpenProtocol (
                ControllerHandle,               // Handle
                &gEfiPciIoProtocolGuid,         // Protocol
                NULL,                           // Interface
                This->DriverBindingHandle,      // AgentHandle
                ControllerHandle,               // ControllerHandle
                EFI_OPEN_PROTOCOL_GET_PROTOCOL  // Attributes
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

**********
**Caution:** It can be dangerous to use this open mode (in which a protocol
does not have to be closed) because a protocol may be removed at any time
without notifying the UEFI Driver that used this mode. This means that a driver
using **EFIOPEN_PROTOCOL_GET_PROTOCOL** may attempt to use a stale protocol
interface pointer that is no longer valid._
**********
**TIP:** Use **EFI_OPEN_PROTOCOL_BY_DRIVER** first, to prevent the protocol
from being removed while a driver is using the protocol.
**********

The `EFI_OPEN_PROTOCOL_GET_PROTOCOL` attribute can then be used to retrieve the
needed protocol interface.

A UEFI driver should be designed to use `EFI_OPEN_PROTOCOL_`BY_DRIVER as its
first choice. However, there are cases where a different UEFI driver has
already opened the protocol that is required by `EFI_OPEN_PROTOCOL_`BY_DRIVER.
In these cases, use `EFI_OPEN_PROTOCOL_`GET_PROTOCOL. This scenario may occur
when protocols are layered on top of each other so that each layer uses the
services of the layer immediately below. Each layer immediately below is opened
with `EFI_OPEN_PROTOCOL_`BY_DRIVER.

If a layer needs to skip a layer to reach a lower-level service, then it is
safe to use `EFI_OPEN_PROTOCOL_`GET_PROTOCOL because the driver is informed
through the layers if the lower-level protocol is removed.

The best example of this case in the EDK II is the FAT driver. The FAT driver
uses the services of the Disk I/O Protocol to access the contents of a mass
storage device. However, the Disk I/O Protocol does not have a flush service.
Only the Block I/O Protocol has a flush service. The Disk I/O driver opens the
Block I/O Protocol `EFI_OPEN_PROTOCOL_BY_DRIVER`, so the FAT driver is also not
allowed to open the Block I/O Protocol `EFI_OPEN_PROTOCOL_BY_DRIVER`. Instead,
the FAT driver must use `EFI_OPEN_PROTOCOL_GET_PROTOCOL`. This method is safe
because the FAT driver is indirectly notified if the Block I/O Protocol is
removed when the Disk I/O Protocol is removed in response to the Block I/O
Protocol being removed.

##### 5.1.3.4.3 Using EFI_OPEN_PROTOCOL_BY_DRIVER

The code fragment in shows the same example as above, but it retrieves the PCI
I/O Protocol using the `EFI_OPEN_PROTOCOL_BY_DRIVER` attribute. When this
attribute is used, the protocol must be closed when the UEFI Driver no longer
requires the services of the PCI I/O Protocol. This example also shows
`CloseProtocol()` being used to close the protocol, which is commonly found in
implementations of `Supported()` and `Stop()`. Notice that the parameters
passed to `CloseProtocol()` are identical to the parameters passed to
`OpenProtocol()` with the _Interface_ and _Attributes_ parameters removed.

###### Example 41-OpenProtocol() EFI_OPEN_PROTOCOL_BY_DRIVER

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/PciIo.h>

EFI_STATUS                                    Status;
EFI_DRIVER_BINDING_PROTOCOL                   *This;
EFI_HANDLE                                    ControllerHandle;

//
// Retrieve PCI I/O Protocol interface on ControllerHandle
//
Status = gBS->OpenProtocol (
                ControllerHandle,             // Handle
                &gEfiPciIoProtocolGuid,       // Protocol
                NULL,                         // Interface
                This->DriverBindingHandle,    // AgentHandle
                ControllerHandle,             // ControllerHandle
                EFI_OPEN_PROTOCOL_BY_DRIVER   // Attributes
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Close PCI I/O Protocol on ControllerHandle
//
Status = gBS->CloseProtocol (
                ControllerHandle,            // Handle
                &gEfiPciIoProtocolGuid,      // Protocol
                This->DriverBindingHandle,   // AgentHandle
                ControllerHandle             // ControllerHandle
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

##### 5.1.3.4.4 Using EFI_OPEN_PROTOCOL_BY_DRIVER | EFI_OPEN_PROTOCOL_EXCLUSIVE

The following code fragment in shows the same example as above, but it
retrieves the PCI I/O Protocol using both the `EFI_OPEN_PROTOCOL_BY_DRIVER`
attribute and the `EFI_OPEN_PROTOCOL_EXCLUSIVE` attribute, which requests any
other UEFI Driver that are using the PCI I/O Protocol release it.

```
There are only a very few instances where EFI_OPEN_PROTOCOL_BY_DRIVER | EFI_OPEN_PROTOCOL_EXCLUSIVE should be used. These are cases where a UEFI driver actually wants to gain exclusive access to a protocol, even if it requires stopping other UEFI drivers to do so.
This combination of attributes is used rarely. One example in the EDK II is the debug port driver that opens the Serial I/O Protocol with the EFI_OPEN_PROTOCOL_BY_DRIVER | EFI_OPEN_PROTOCOL_EXCLUSIVE attribute. This attribute allows a debugger to take control of a serial port even if it is already being used as a console device. If this device is the only console device in the system, then the user loses the only console device when the debug port driver is started.
```

**********
**Caution:** This open mode can be **dangerous** if the system requires the
services produced by the UEFI drivers that are stopped.
**********

###### Example 42-OpenProtocol() EFI_OPEN_PROTOCOL_BY_DRIVER

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/PciIo.h>

EFI_STATUS                                      Status;
EFI_DRIVER_BINDING_PROTOCOL                     *This;
EFI_HANDLE                                      ControllerHandle;

//
// Retrieve PCI I/O Protocol interface on ControllerHandle
//
Status = gBS->OpenProtocol (
                ControllerHandle,               // Handle
                &gEfiPciIoProtocolGuid,         // Protocol
                NULL, // Interface
                This->DriverBindingHandle,      // AgentHandle
                ControllerHandle,               // ControllerHandle
                EFI_OPEN_PROTOCOL_BY_DRIVER |   // Attributes
                EFI_OPEN_PROTOCOL_EXCLUSIVE
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Close PCI I/O Protocol on ControllerHandle
//
Status = gBS->CloseProtocol (
                ControllerHandle,              // Handle
                &gEfiPciIoProtocolGuid,        // Protocol
                This->DriverBindingHandle,     // AgentHandle
                ControllerHandle               // ControllerHandle
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

**EFI_OPEN_PROTOCOL_EXCLUSIVE**

##### 5.1.3.4.5 Using EFI_OPEN_PROTOCOL_BY_CHILD_CONTROLLER

The code fragment below shows an example that may be used by a UEFI Bus Driver
that produces child handles. This specific example shows the PCI bus driver
creating a child handle, opening the PCI Root Bridge I/O Protocol using the
`EFI_OPEN_PROTOCOL_BY_CHILD_CONROLLER` attribute on behalf of a child PCI
controller that the PCI bus driver created, closing the PCI Root Bridge I/O
Protocol, and destroying the child handle. These operations are typically
spread across the Start() and Stop() functions.

###### Example 43-OpenProtocol() **EFI_OPEN_PROTOCOL_BY_CHILD_CONTROLLER**

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Protocol/DriverBinding.h>
#include <Protocol/PciRootBridgeIo.h>
#include <Protocol/DevicePath.h>
#include <Protocol/PciIo.h>

EFI_STATUS                                            Status;
EFI_DRIVER_BINDING_PROTOCOL                           *This;
EFI_PCI_ROOT_BRIDGE_IO_PROTOCOL                       *PciRootBridgeIo;
EFI_DEVICE_PATH_PROTOCOL                              *DevicePath;
EFI_PCI_IO_PROTOCOL                                   *PciIo;
EFI_HANDLE                                            ControllerHandle;
EFI_HANDLE                                            ChildHandle;

//
// Create new child handle
//
ChildHandle = NULL;
Status = gBS->InstallMultipleProtocolInterfaces (
                &ChildHandle,
                &gEfiDevicePathProtocolGuid,
                DevicePath,
                &gEfiPciIoProtocolGuid,
                PciIo,
                NULL
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Open parent PCI Root Bridge I/O Protocol
//
Status = gBS->OpenProtocol (
                ControllerHandle,                     //Handle
                &gEfiPciRootBridgeIoProtocolGuid,     //Protocol
                (VOID **)&PciRootBridgeIo,            //Interface
                This->DriverBindingHandle,            //AgentHandle
                ChildHandle,                          //ControllerHandle
                EFI_OPEN_PROTOCOL_BY_CHILD_CONTROLLER //Attributes
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Close parent PCI Root Bridge I/O Protocol
//
Status = gBS->CloseProtocol (
                ControllerHandle,                     // Handle
                &gEfiPciRootBridgeIoProtocolGuid,     // Protocol
                This->DriverBindingHandle,            // AgentHandle
                ChildHandle                           // ControllerHandle
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Destroy child handle
//
Status = gBS->UninstallMultipleProtocolInterfaces (
                ChildHandle,
                &gEfiDevicePathProtocolGuid,
                DevicePath,
                &gEfiPciIoProtocolGuid,
                PciIo,
                NULL
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```

#### 5.1.3.5 OpenProtocolInformation()

This service retrieves the list of agents currently using a specific protocol
interface installed on a handle in the handle database. An agent may be a UEFI
Driver or a UEFI Application using the services of a protocol interface. The
`OpenProtocol()` service adds agents to the list, and the `CloseProtocol()`
service removes agents from the list. The return buffer from this service is
allocated using `AllocatePool()`. To prevent memory leaks, the caller must free
the return buffer with `FreePool()` when it no longer needs it.

The UEFI Shell command `openinfo` uses this service to view the results from
OpenProtocolInformation() for any protocol installed into the handle database.
It is very useful when debugging UEFI Drivers to evaluate the state of
protocols the drivers consume and produce in the handle database and to verify
that the UEFI Driver is using `OpenProtocol()` and `CloseProtocol()` properly.

A UEFI Driver may use this service to find the list of controllers the UEFI
Driver is managing or the list of child handles that the UEFI driver has
produced in previous calls to the `Start()`. A UEFI Driver may also choose to
keep track of this type of information itself and not use the Protocol Handler
Services to retrieve this type of information.

The following code fragment uses `LocateHandleBuffer()` to retrieve the list of
handles that support the PCI Root Bridge I/O Protocol. It then uses
`OpenProtocolInformation()` on the first handle that supports the PCI Root
Bridge I/O Protocol to retrieve information on all the agents that are using
that specific PCI Root Bridge I/O Protocol instance. This example then loops
through all the consumers of that PCI Root Bridge I/O Protocol and counts the
number of handles that have opened the PCI Root Bridge I/O Protocol instance
with an open mode of `EFI_OPEN_PROTOCOL_BY_CHILD_CONTROLLER`. This open mode
indicates that the agent is a child handle. The result is the total number of
PCI controllers that are attached to that specific PCI Root Bridge I/O Protocol
instance.

###### Example 44-Count child handles using OpenProtocolInformation()

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/MemoryAllocationLib.h>
#include <Protocol/PciRootBridgeIo.h>

EFI_STATUS                            Status;
UINTN                                 HandleCount;
EFI_HANDLE                            *HandleBuffer;
EFI_HANDLE                            ControllerHandle;
EFI_OPEN_PROTOCOL_INFORMATION_ENTRY   *OpenInfo;
UINTN                                 EntryCount;
UINTN                                 Index;
UINT32                                Attributes;
UINTN                                 NumberOfChildren;

//
// Retrieve array of handles that support the USB I/O Protocol
//
Status = gBS->LocateHandleBuffer (
                ByProtocol,
                &gEfiPciRootBridgeIoProtocolGuid,
                NULL,
                &HandleCount,
                &HandleBuffer
                );
if (EFI_ERROR (Status)) {
  return Status;
}
if (HandleCount == 0) {
  return EFI_NOT_FOUND;
}

//
// Assign ControllerHandle to the first handle in the array
//
ControllerHandle = HandleBuffer[0];

//
// Free the array of handles
//
FreePool (HandleBuffer);

//
// Retrieve information about how the PCI Root Bridge I/O Protocol
// instance on ControllerHandle is being used.
//
Status = gBS->OpenProtocolInformation (
                ControllerHandle,
                &gEfiPciRootBridgeIoProtocolGuid,
                &OpenInfo,
                &EntryCount
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Count the number child handles that are currently using the PCI Root
// Bridge I/O Protocol on ControllerHandle children
//
for (Index = 0, NumberOfChildren = 0; Index < EntryCount; Index++) {
  Attributes = OpenInfo[Index].Attributes;
  if ((Attributes & EFI_OPEN_PROTOCOL_BY_CHILD_CONTROLLER) != 0) {
    NumberOfChildren++;
  }
}

//
// Free the buffer allocated by OpenProtocolInformation()
//
FreePool (OpenInfo);
```
