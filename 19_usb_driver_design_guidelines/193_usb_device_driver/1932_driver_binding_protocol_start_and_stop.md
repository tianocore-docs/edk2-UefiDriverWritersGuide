<!--- @file
  19.3.2 Driver Binding Protocol Start() and Stop()

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

### 19.3.2 Driver Binding Protocol Start() and Stop()

The `Start()` service of the Driver Binding Protocol for a USB device driver
opens the USB I/O Protocol with an attribute of `EFI_OPEN_PROTOCOL_BY_DRIVER.`
The service then installs the I/O abstraction protocol for the USB device or
host controller onto the handle on which the `EFI_USB_IO_PROTOCOL` is installed.

#### 19.3.2.1 Example using a USB mass storage device

This discussion provides detailed guidance on how to implement a USB device
driver. It uses a USB mass storage device as an example. For example, suppose
this mass storage device has the following four endpoints:
* One control endpoint
* One interrupt endpoint
* Two bulk endpoints

For the interrupt endpoint, it is synchronous. For the bulk endpoints, one is
an input endpoint and the other is an output endpoint. The following
discussions cover how to implement the `Start()` and `Stop()` driver binding
protocol services and UEFI Block I/O protocol.

This example shows a portion of the private context data structure for a USB
mass storage device driver. See _Chapter 8_ of this guide for more information
about design guidelines for private context data structures.

###### Example 207-USB mass storage driver private context data structure

```c
#include <Uefi.h>
#include <Protocol/UsbIo.h>
#include <Protocol/BlockIo.h>

typedef struct {
  UINT64 Signature;
  
  EFI_BLOCK_IO_PROTOCOL           BlockIO;
  EFI_USB_IO_PROTOCOL             *UsbIo;
  
  EFI_USB_INTERFACE_DESCRIPTOR    InterfaceDescriptor;
  EFI_USB_ENDPOINT_DESCRIPTOR     BulkInEndpointDescriptor;
  EFI_USB_ENDPOINT_DESCRIPTOR     BulkOutEndpointDescriptor;
  EFI_USB_ENDPOINT_DESCRIPTOR     InterruptEndpointDescriptor;
  
} USB_MASS_STORAGE_DEVICE;
```

#### 19.3.2.2 Example implementing Driver Binding Start()

The following steps are performed in the Driver Binding Protocol `Start()`
service.
1. Open the USB I/O Protocol on _ControllerHandle_
   `EFI_OPEN_PROTOCOL_BY_DRIVER`.
2. Get the interface descriptor using the ##### EFI_USB_IO_PROTOCOL.UsbGetInterfaceDescriptor() service.
3. `Prepare the private data structure.` This private data structure is in type
   `USB_MASS_STORAGE_DEVICE` and has fields for the interface descriptor,
   endpoint descriptor, and others. This step allocates memory for the private
   data structure and does the required initializations-for example, setting up
   the _Signature_, _UsbIo_, and _InterfaceDescriptor_ fields.
4. `Parse the interface descriptor.` In this step, the USB device driver parses
   the _InterfaceDescriptor_ that was obtained in step 2, and verifies that all
   bulk and interrupt endpoints exit. The _NumEndpoints_ field in
  _InterfaceDescriptor_ indicates how many endpoints are in this USB interface.
  Next, the endpoint descriptors are retrieved one by one by using the
  `UsbGetEndpointDescriptor()` service. Then, the _Attributes_ and
  _EndpointAddress_ fields in _EndpointDescriptor_ are evaluated to determine the
  type of endpoint.
5. Install the Block I/O protocol.

#### 19.3.2.3 Example implementing Driver Binding Stop()

The Driver Binding Protocol `Stop()` service performs the reverse steps of the
`Start()` service. Continuing with the previous example, the `Stop()` service
uninstalls the Block I/O Protocol and closes the USB I/O Protocol. It also
frees various allocated resources such as the private data structure.
