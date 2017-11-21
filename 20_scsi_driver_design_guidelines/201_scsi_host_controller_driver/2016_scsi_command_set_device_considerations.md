<!--- @file
  20.1.6 SCSI command set device considerations

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

### 20.1.6 SCSI command set device considerations

Extended SCSI Pass Thru Protocol defines a method to directly access SCSI
devices. This protocol provides interfaces that allow a generic driver to produce the
Block I/O Protocol for SCSI mass storage devices and allows a UEFI utility to
issue commands to any SCSI device. The main reason to provide such an access is
to enable S.M.A.R.T. functionality during POST (i.e., issuing Mode Sense, Mode
Select, and Log Sense to SCSI devices). This enabling is accomplished using the
generic interfaces that are defined in Extended SCSI Pass Thru Protocol. The
implementation of this protocol also enables additional functionality in the
future without modifying the SCSI drivers that are built on top of the SCSI
host controller driver. Furthermore, Extended SCSI Pass Thru Protocol is not
limited to SCSI adapters. It is applicable to all channel technologies that use
SCSI commands such as ATAPI, iSCSI, and Fibre Channel. This section shows some
examples that demonstrate how to implement Extended SCSI Pass Thru Protocol on
SCSI command set-compatible technology.

#### 20.1.6.1 ATAPI

This section provides guidance on how to implement the Extended SCSI Pass Thru
Protocol for ATAPI devices.

Decoding the Target and Lun pair uses the intrinsic property of the technology
or device. For ATAPI, only four devices are supported, so the Target and Lun
pair can be decoded by determining the IDE channel (primary/secondary) and IDE
device (master/slave).

If the corresponding technology or device supports the channel reset operation,
use it to implement `EFI_EXT_SCSI_PASS_THRU_PROTOCOL.ResetChannel()`; if not,
it may be implemented by resetting all attached devices on the channel and
re-enumerating them.

In the `EFI_EXT_SCSI_PASS_THRU_PROTOCOL.BuildDevicePath()` function, all target
devices should be built on a node based on the channel knowledge. The example
below shows how to build a device path node for an ATAPI device.

###### Example 216-Building Device Path for ATAPI Device

```c
#include <Uefi.h>
#include <Protocol/ScsiPassThruExt.h> 
#include <Protocol/DevicePath.h>
#include <Library/DevicePathLib.h>

EFI_STATUS
EFIAPI
AbcBuildDevicePath (
  IN     EFI_EXT_SCSI_PASS_THRU_PROTOCOL  *This,
  IN     UINT8                            *Target,
  IN     UINT64                           Lun,
  IN OUT EFI_DEVICE_PATH_PROTOCOL         **DevicePath
  )
{
  ATAPI_DEVICE_PATH  *Node;

  Node = (ATAPI_DEVICE_PATH *)CreateDeviceNode (
                                MESSAGING_DEVICE_PATH,
                                MSG_ATAPI_DP,
                                sizeof (ATAPI_DEVICE_PATH)
                                );
  if (Node == NULL) {
    return EFI_OUT_OF_RESOURCES;
  }
  
  Node->PrimarySecondary = (UINT8)(*Target >> 1);
  Node->SlaveMaster = (UINT8)(*Target & 0x01);
  Node->Lun = (UINT16)Lun;
  
  *DevicePath = (EFI_DEVICE_PATH_PROTOCOL *)Node;
  
  return EFI_SUCCESS;
}
```

For the most important function, `EFI_EXT_SCSI_PASS_THRU_PROTOCOL.PassThru()`,
it should be implemented by technology-dependent means. In this example, ATAPI
supports a SCSI command using the IDE "Packet" command. Because the IDE command
is delivered through a group of I/O registers, the main body of the
implementation is filling the SCSI command structure to these I/O registers and
then waiting for the command completion. A complete code example for the
blocking I/O `EFI_EXT_SCSI_PASS_THRU_PROTOCOL` services can be found in the EDK II
`MdeModulePkg` in the directory `MdeModulePkg\Bus\Ata\AtaAtapiPassThru`.

For the non-blocking I/O `EFI_EXT_SCSI_PASS_THRU_PROTOCOL` function, the SCSI
driver submits the SCSI command and returns. It may choose to poll an internal
timer event to check whether the submitted command completes its execution. If
so, it should signal the client event. The UEFI firmware then schedules the
notification function of the client event to be called.

The following example shows a sample non-blocking Extended SCSI Pass Thru
Protocol implementation.

###### Example 217-Non-Blocking Extended SCSI Pass-Thru Protocol Implementation

```c
#include <Uefi.h>
#include <Protocol/ScsiPassThruExt.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/UefiLib.h>

#define ATAPI_SCSI_PASS_THRU_DEV_SIGNATURE SIGNATURE_32('A','t','a','S')

typedef struct {
  UINTN Signature;
  EFI_HANDLE Handle;
  EFI_EXT_SCSI_PASS_THRU_PROTOCOL ScsiPassThru;
  EFI_EXT_SCSI_PASS_THRU_MODE ScsiPassThruMode;
  EFI_EVENT ClientEvent;
} ATAPI_SCSI_PASS_THRU_DEV;

#define ATAPI_SCSI_PASS_THRU_DEV_FROM_THIS(a) \
  CR(a, ATAPI_SCSI_PASS_THRU_DEV, ScsiPassThru, ATAPI_SCSI_PASS_THRU_DEV_SIGNATURE)
  
VOID
EFIAPI
AbcScsiPassThruPollEventNotify (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  ATAPI_SCSI_PASS_THRU_DEV  *AtapiScsiPrivate;
  BOOLEAN                   CommandCompleted;
  
  ASSERT (Context);
  AtapiScsiPrivate = (ATAPI_SCSI_PASS_THRU_DEV *)Context;
  CommandCompleted = FALSE;

  //
  // Use specific knowledge to identify whether command execution
  // completes or not. If so, set CommandCompleted as TRUE.
  //
  // ......................
 
 if (CommandCompleted) {
    //
    // Get client event handle from private context data structure.
    // Signal it.
    //
    gBS->SignalEvent (AtapiScsiPrivate->ClientEvent);
  }
}

EFI_STATUS
EFIAPI
AbcScsiPassThru (
  IN     EFI_EXT_SCSI_PASS_THRU_PROTOCOL             *This,
  IN     UINT8                                       *Target,
  IN     UINT64                                      Lun,
  IN OUT EFI_EXT_SCSI_PASS_THRU_SCSI_REQUEST_PACKET  *Packet,
  IN     EFI_EVENT                                   Event     OPTIONAL
  )
{
  ATAPI_SCSI_PASS_THRU_DEV  *AtapiScsiPrivate;
  EFI_EVENT                 InternalEvent;
  EFI_STATUS                Status;
  
  AtapiScsiPrivate = ATAPI_SCSI_PASS_THRU_DEV_FROM_THIS (This);
  
  //
  // Do parameter checking required by UEFI Specification
  //
  //..................................
  
  //
  // Create internal timer event in order to poll the completion.
  // The event can also be created outside of this function to
  // avoid frequent event construction/destruction.
  //
  Status = gBS->CreateEvent (
                  EVT_TIMER | EVT_NOTIFY_SIGNAL,
                  TPL_CALLBACK,
                  AbcScsiPassThruPollEventNotify,
                  AtapiScsiPrivate,
                  &InternalEvent
                  );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // Signal the polling event every 200 ms. Select the interval
  // according to the specific requirement and technology.
  //
  Status = gBS->SetTimer (
                  InternalEvent,
                  TimerPeriodic,
                  EFI_TIMER_PERIOD_MILLISECONDS (200)
                  );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // Submit SCSI I/O command through IDE I/O registers and return
  //
  // . .
  //
  
  return Status;
}
```
