<!--- @file
  5.2.2 ReinstallProtocolInterface()

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

### 5.2.2 ReinstallProtocolInterface()

This service should be used only to indicate media change events and when a
device path is modified or updated. Some examples of when this service must be
used are: - A UEFI Driver that produces the Block I/O Protocol for a removable
media device when the media in a removable media device is changed (i.e.
Floppy, CD, DVD).

* A UEFI Driver that produces the Serial I/O Protocol when the attributes are
  modified using `SetAttributes()`

* A UEFI Driver that produces the Simple Network Protocol when the MAC address
  of the network interface is modified using `StationAddress()`.

Internally, this service performs the following series of actions:

1. `UninstallProtocolInterface()`, which may cause `DisconnectController()` to
   be called

2. `InstallProtocolInterface()`

3. `ConnectController()` to allow controllers that had to release the protocol
   a chance to connect to it again

**********
**Caution:** This service may induce reentrancy if a driver makes a request
that requires a UEFI
**********

_Driver for a parent device to call `ReinstallProtocolInterface()`. In this
case, the driver making the request may not realize that the request causes the
driver to be completely stopped and completely restarted when the request to
the parent device is made._

_For example, consider a terminal driver that wants to change the baud rate on
the serial port. The baud rate is changed with a call to the Serial I/O
Protocol's_

_`SetAttributes()`. This call changes the baud rate, which is reflected in the
device path of the serial device, so the Device Path Protocol is reinstalled by
the `SetAttributes()` service. This reinstallation forces the terminal driver
to be disconnected. The terminal driver then attempts to connect to the serial
device again, but the baud rate is the one that the terminal driver expects, so
the terminal driver does not need to set the baud rate again._

_Any consumer of a protocol that supports this media change concept needs to be
aware that the protocol can be reinstalled at any time and that care must be
taken in the design of drivers that use this type of protocol._

The following code fragments in _Example 61_ show what a UEFI driver that
produces the Block I/O Protocol should do when the media in a removable media
device is changed. The exact same protocol is reinstalled onto the controller
handle. The specific action that detects if the media is not included in this
code fragment. The original Block I/O Media structure is copied so it can be
compared with the Block I/O Media structure after the media change detection
logic is executed. The Block I/O Protocol is reinstalled if the Media ID is
different, if the size of blocks on the mass storage device has changed, if the
number of blocks on the mass storage device has changed, if the present status
has changed, or if the media has changed from read-only to read-write or vice
versa.

###### Example 61-Reinstall Block I/O Protocol for media change

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/BaseMemoryLib.h>

EFI_STATUS Status;
EFI_HANDLE ControllerHandle;
EFI_BLOCK_IO_PROTOCOL *BlockIo;
EFI_BLOCK_IO_MEDIA OldMedia;

//
// Make a copy of the current Block I/O Media structure
//
CopyMem (&OldMedia, &(BlockIo->Media), sizeof (EFI_BLOCK_IO_MEDIA));

//
// Perform driver specific action(s) required to detect if the
// media has been changed and update Block I/O Media structure.
//
//
// Detect whether it is necessary to reinstall the Block I/O Protocol.
//
if ((BlockIo->Media->MediaId != OldMedia.MediaId) ||
    (BlockIo->Media->MediaPresent != OldMedia.MediaPresent) ||
    (BlockIo->Media->ReadOnly != OldMedia.ReadOnly) ||
    (BlockIo->Media->BlockSize != OldMedia.BlockSize) ||
    (BlockIo->Media->LastBlock != OldMedia.LastBlock) ) {
	
  Status = gBS->ReinstallProtocolInterface (
                  ControllerHandle,
                  &gEfiBlockIoProtocolGuid,
                  BlockIo,
                  BlockIo
                  );
  if (EFI_ERROR (Status)) {
    return Status;
  }
}
```

The code fragments below show the Device Path Protocol for a Serial I/O device
being reinstalled because the serial communication parameters that are
expressed in a UART Device Path Node have been modified in a call to the `SetAttributes()` service
of the Serial I/O Protocol.

###### Example 62-Reinstall Device Path Protocol for Serial I/O attributes change

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>

EFI_STATUS Status;
EFI_HANDLE ControllerHandle;
EFI_DEVICE_PATH_PROTOCOL *DevicePath;

//
// Retrieve the Device Path Protocol instance on ControllerHandle
//
Status = gBS->OpenProtocol (
                ControllerHandle,
                &gEfiDevicePathProtocolGuid,
                (VOID **)&DevicePath,
                gImageHandle,
                ControllerHandle,
                EFI_OPEN_PROTOCOL_GET_PROTOCOL
                );
if (EFI_ERROR (Status)) {
  return Status;
}

//
// Check to see if the UART parameters have been modified
// and update UART node of DevicePath
//
//
//
//
Status = gBS->ReinstallProtocolInterface (
                ControllerHandle,
                &gEfiDevicePathProtocolGuid,
                DevicePath,
                DevicePath
                );
if (EFI_ERROR (Status)) {
  return Status;
}
```
