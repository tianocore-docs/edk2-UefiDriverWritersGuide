<!--- @file
  19.3.3 I/O Protocol Implementations

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

### 19.3.3 I/O Protocol Implementations

The following examples reference a private context data structure called
USB_MOUSE_DEV. The example below shows the portion of this data structure
required for the other examples.

###### Example 208-USB Mouse Private Context Data Structure

```c
#include <Uefi.h>
#include <Protocol/UsbIo.h>
#include <Protocol/SimplePointer.h>

#define USB_MOUSE_DEV_PRIVATE_DATA_SIGNATURE SIGNATURE_32('U','s','b','M')

typedef struct {
  UINTN                        Signature;
  EFI_USB_IO_PROTOCOL          *UsbIo;
  EFI_SIMPLE_POINTER_PROTOCOL  SimplePointer;
  EFI_SIMPLE_POINTER_STATE     State;
  EFI_USB_ENDPOINT_DESCRIPTOR  IntEndpointDescriptor;
  BOOLEAN                      StateChanged;
} USB_MOUSE_DEV;

#define USB_MOUSE_DEV_FROM_MOUSE_PROTOCOL(a) \
  CR(a, USB_MOUSE_DEV, SimplePointer, USB_MOUSE_DEV_PRIVATE_DATA_SIGNATURE)
```

This example uses the USB mouse driver to shows how the USB device driver can
setup asynchronous interrupt transfers from the Driver Binding Protocol
`Start()` service.

###### Example 209-Setup asynchronous interrupt transfer for USB mouse driver

```c
#include <Uefi.h>
#include <Protocol/UsbIo.h>

Status = UsbIo->UsbAsyncInterruptTransfer (
                  UsbIo,
                  EndpointAddr,
                  TRUE,
                  PollingInterval,
                  PacketSize,
                  OnMouseInterruptComplete,
                  UsbMouseDevice
                  );
```

The next example shows the corresponding asynchronous interrupt transfer
callback function called `OnMouseInterruptComplete()`. In this function, if the
passing _Result_ parameter indicates an error, it clears the endpoint error
status, unregisters the previous asynchronous interrupt transfer, and initiates
another asynchronous interrupt transfer. If there is no error, it set the mouse
state change indicator to `TRUE` and put the data that is read into the
appropriate data structure.

###### Example 210-Completing an asynchronous interrupt transfer

```c
#include <Uefi.h>
#include <Protocol/UsbIo.h>
#include <Library/UefiUsbLib.h>

EFI_STATUS
EFIAPI
OnMouseInterruptComplete (
  IN VOID              *Data,
  IN UINTN             DataLength,
  IN VOID              *Context,
  IN UINT32            Result
  )
{
  USB_MOUSE_DEV        *UsbMouseDev;
  EFI_USB_IO_PROTOCOL  *UsbIo;
  UINT8                EndpointAddr;
  UINT32               UsbResult;
  
  UsbMouseDev = (USB_MOUSE_DEV *)Context;
  UsbIo = UsbMouseDev->UsbIo;
  
  if (Result != EFI_USB_NOERROR) {
    if ((Result & EFI_USB_ERR_STALL) == EFI_USB_ERR_STALL) {
      EndpointAddr = UsbMouseDev->IntEndpointDescriptor.EndpointAddress;
      UsbClearEndpointHalt (
        UsbIo,
        EndpointAddr,
        &UsbResult
        );
    }

    //
    // Unregister previous asynchronous interrupt transfer
    //
    UsbIo->UsbAsyncInterruptTransfer (
             UsbIo,
             UsbMouseDev->IntEndpointDescriptor.EndpointAddress,
             FALSE,
             0,
             0,
             NULL, NULL
             );
 
    //
    // Initiate a new asynchronous interrupt transfer
    //
    UsbIo->UsbAsyncInterruptTransfer (
             UsbIo,
             UsbMouseDev->IntEndpointDescriptor.EndpointAddress,
             TRUE,
             UsbMouseDev->IntEndpointDescriptor.Interval,
             UsbMouseDev->IntEndpointDescriptor.MaxPacketSize,
             OnMouseInterruptComplete,
             UsbMouseDev
             );
    return EFI_DEVICE_ERROR;
  }
  
  UsbMouseDev->StateChanged = TRUE;
  
  //
  // Parse HID data package
  // and extract mouse movements and coordinates to UsbMouseDev
  //
  // . .
  //
  
  return EFI_SUCCESS;
}
```

This example shows the `GetMouseState()` service of the Simple Pointer Protocol
that the USB mouse driver produces. `GetMouseState()`does not initiate any
asynchronous interrupt transfer requests. It simply checks the mouse state
change indicator. If there is mouse input, it copies the mouse input to the
passing _MouseState_ data structure.

###### Example 211-Retrieving pointer movement

```c
#include <Uefi.h>
#include <Protocol/UsbIo.h>
#include <Protocol/SimplePointer.h>
#include <Library/BaseMemoryLib.h>

EFI_STATUS
EFIAPI
GetMouseState (
  IN  EFI_SIMPLE_POINTER_PROTOCOL  *This,
  OUT EFI_SIMPLE_POINTER_STATE     *MouseState
  )
{
  USB_MOUSE_DEV  *MouseDev;
  
  MouseDev = USB_MOUSE_DEV_FROM_MOUSE_PROTOCOL (This);
  
  if (MouseDev->StateChanged == FALSE) {
    return EFI_NOT_READY;
  }
  
  CopyMem (MouseState, &MouseDev->State, sizeof (EFI_SIMPLE_POINTER_STATE));
  
  //
  // Clear previous move state
  //
  // . .
  //
  
  return EFI_SUCCESS;
}
```
