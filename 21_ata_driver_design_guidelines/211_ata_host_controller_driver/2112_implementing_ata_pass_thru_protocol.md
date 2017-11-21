<!--- @file
  21.1.2 Implementing ATA Pass Thru Protocol

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

### 21.1.2 Implementing ATA Pass Thru Protocol

EFI_ATA_PASS_THRU_PROTOCOL allows information about a ATA target to be
collected and allows ATA Request Packets to be sent to any ATA devices
connected to the ATA host controller, even if those devices are not boot
devices. This protocol is attached to the device handle of the ATA host
controller in a system that the protocol supports and can be used for
diagnostics. It may also be used to build a block I/O driver for ATA hard
drives allowing those devices to be used as boot devices. The ATA Pass Thru
Protocol is usually implemented in the file AtaPassThru.c. Appendix A contains
a template for the ATA Pass Thru Protocol.

###### Example 220-ATA Pass Thru Protocol

```c
typedef struct _EFI_ATA_PASS_THRU_PROTOCOL EFI_ATA_PASS_THRU_PROTOCOL;

struct _EFI_ATA_PASS_THRU_PROTOCOL {
  EFI_ATA_PASS_THRU_MODE               *Mode;
  EFI_ATA_PASS_THRU_PASSTHRU           PassThru;
  EFI_ATA_PASS_THRU_GET_NEXT_PORT      GetNextPort;
  EFI_ATA_PASS_THRU_GET_NEXT_DEVICE    GetNextDevice;
  EFI_ATA_PASS_THRU_BUILD_DEVICE_PATH  BuildDevicePath;
  EFI_ATA_PASS_THRU_GET_DEVICE         GetDevice;
  EFI_ATA_PASS_THRU_RESET_PORT         ResetPort;
  EFI_ATA_PASS_THRU_RESET_DEVICE       ResetDevice;
};
```

For a detailed description of `EFI_ATA_PASS_THRU_PROTOCOL`, see the ATA Pass
Thru Protocol section of the _UEFI Specification_.

Before implementing ATA Pass Thru Protocol, the ATA host controller driver
configures the ATA host controller to a defined state. In practice, the ATA
host controller maps a set of ATA host controller registers in I/O or
memory-mapped I/O space. Although the detailed layout or functions of these
registers vary from one ATA host controller to another, the ATA host controller
driver uses specific knowledge to set up the proper ATA mode and configure the
timing registers for the current mode. Other considerations include DMA engine
and interrupt initialization, among others.

All the hardware-related settings must be completed before any ATA Pass Thru
Protocol functions are called. The initialization is usually performed in the
Driver Binding Protocol's `Start()` function of the ATA host controller driver
prior to installing the ATA Pass Thru Protocol instance into the Handle
Database.

`EFI_ATA_PASS_THRU_PROTOCOL.`_Mode_ is a structure that describes the intrinsic
attributes of the ATA Pass Thru Protocol instance. Note that a non-RAID ATA
host controllers set both the physical and logical attributes. A physical
channel on the RAID sets only the physical attribute, and the logical channel
on the RAID adapter sets only the logical attribute. If the channel supports
non-blocking I/O, the non-blocking attribute is also set. The example below
shows how to set those attributes on a non-RAID ATA host controller that
supports non-blocking I/O.

###### Example 221-ATA Pass Thru Mode Structure

```c
//
// The channel does support nonblocking I/O
//
AtaPassThruMode.Attributes = EFI_ATA_PASS_THRU_ATTRIBUTES_PHYSICAL |
                             EFI_ATA_SCSI_PASS_THRU_ATTRIBUTES_LOGICAL |
                             EFI_ATA_SCSI_PASS_THRU_ATTRIBUTES_NONBLOCKIO;
//
// Do not have any alignment requirement
//
AtaPassThruMode.IoAlign = 0;
```

The example below shows how to set the ATA _Mode_ structures for an ATA host
controller that provides RAID capabilities and produced an ATA Pass Thru
Protocol instance for accessing the physical drives and another ATA Pass Thru
Protocol instance for accessing the logical drives.

###### Example 222-SCSI Pass Thru Mode Structures for RAID SCSI adapter

```c
//
// ...... Physical Channel ......
//
AtaPassThruMode.Attributes = EFI_ATA_PASS_THRU_ATTRIBUTES_PHYSICAL |
                             EFI_ATA_PASS_THRU_ATTRIBUTES_NONBLOCKIO;
AtaPassThruMode.IoAlign = 0;

//
// ...... Logical Channel ......
//

AtaPassThruMode.Attributes = EFI_ATA_PASS_THRU_ATTRIBUTES_LOGICAL |
                             EFI_ATA_PASS_THRU_ATTRIBUTES_NONBLOCKIO;
AtaPassThruMode.IoAlign = 0;
```

The `EFI_ATA_PASS_THRU_PROTOCOL.GetNextPort()` and `EFI_ATA_PASS_THRU_PROTOCOL.GetNextDevice()` functions provide the ability to enumerate all the ATA devices. `EFI_ATA_PASS_THRU_PROTOCOL.BuildDevicePath()`
function facilitates the construction of an ATA device path.

The `EFI_ATA_PASS_THRU_PROTOCOL.PassThru()` function is the most important
function when implementing ATA Pass Thru Protocol and it performs the following:
* Initialize the internal register for command/data transfer.
* Put valid ATA commands into hardware-specific memory or register locations.
* Start the transfer.
* Optionally wait for completion of the execution.

The better error handling mechanism in this function helps to develop a more
robust driver. Although most ATA host controllers support both blocking and
non-blocking data transfers, some may only support blocking transfers.
