<!--- @file
  20.1.5 Implementing Extended SCSI Pass Thru Protocol

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

### 20.1.5 Implementing Extended SCSI Pass Thru Protocol

`EFI_EXT_SCSI_PASS_THRU_PROTOCOL` allows information about a SCSI channel to be
collected and allows SCSI Request Packets to be sent to any SCSI devices on a
SCSI channel, even if those devices are not boot devices. This protocol is
attached to the device handle of each SCSI channel in a system that the
protocol supports and can be used for diagnostics. It may also be used to build
a block I/O driver for SCSI hard drives and SCSI CD-ROM or DVD drives to allow
those devices to become boot devices.

The Extended SCSI Pass Thru Protocol is usually implemented in the file `ExtScsiPassThru.c`. Appendix A contains a template for the Extended SCSI Pass Thru Protocol.

###### Example 212-Extended SCSI Pass Thru Protocol

```c
typedef struct _EFI_EXT_SCSI_PASS_THRU_PROTOCOL EFI_EXT_SCSI_PASS_THRU_PROTOCOL;

///
/// The EFI_EXT_SCSI_PASS_THRU_PROTOCOL provides information about a SCSI channel
/// and the ability to send SCI Request Packets to any SCSI device attached to
/// that SCSI channel. The information includes the Target ID of the host
/// controller on the SCSI channel and the attributes of the SCSI channel.
///
struct _EFI_EXT_SCSI_PASS_THRU_PROTOCOL {
  ///
  /// A pointer to the EFI_EXT_SCSI_PASS_THRU_MODE data for this SCSI channel.
  ///
  EFI_EXT_SCSI_PASS_THRU_MODE                 *Mode;
  EFI_EXT_SCSI_PASS_THRU_PASSTHRU             PassThru;
  EFI_EXT_SCSI_PASS_THRU_GET_NEXT_TARGET_LUN  GetNextTargetLun;
  EFI_EXT_SCSI_PASS_THRU_BUILD_DEVICE_PATH    BuildDevicePath;
  EFI_EXT_SCSI_PASS_THRU_GET_TARGET_LUN       GetTargetLun;
  EFI_EXT_SCSI_PASS_THRU_RESET_CHANNEL        ResetChannel;
  EFI_EXT_SCSI_PASS_THRU_RESET_TARGET_LUN     ResetTargetLun;
  EFI_EXT_SCSI_PASS_THRU_GET_NEXT_TARGET      GetNextTarget;
};
```

For a detailed description of `EFI_EXT_SCSI_PASS_THRU_PROTOCOL`, see the
section in the _UEFI Specification_ on SCSI Driver Models and Bus Support_._

Before implementing Extended SCSI Pass Thru Protocol, the SCSI host controller
driver configures the SCSI host controller to a defined state. In practice, the
SCSI adapter maps a set of SCSI host controller registers in I/O or
memory-mapped I/O space. Although the detailed layout or functions of these
registers vary from one SCSI hardware to another, the SCSI host controller
driver uses specific knowledge to set up the proper SCSI working mode (SCSI-I,
SCSI-II, Ultra SCSI, and so on) and configure the timing registers for the
current mode. Other considerations include parity options, DMA engine and
interrupt initialization, among others.

All the hardware-related settings must be completed before any Extended SCSI
Pass

Thru Protocol functions are called. The initialization is usually performed in
the Driver Binding Protocol's `Start()` function of the SCSI host controller
driver prior to installing the Extended SCSI Pass Thru Protocol instance into
the Handle Database.

`EFI_EXT_SCSI_PASS_THRU_PROTOCOL.`_Mode_ is a structure that describes the
intrinsic attributes of Extended SCSI Pass Thru Protocol instance. Note that a
non-RAID SCSI channel sets both the physical and logical attributes. A physical
channel on the RAID adapter only sets the physical attribute, and the logical
channel on the RAID adapter only sets the logical attribute. If the channel
supports non-blocking I/O, the nonblocking attribute is also set. The example
below shows how to set those attributes on a non-RAID SCSI adapter that
supports non-blocking I/O.

###### Example 213-SCSI Pass Thru Mode Structure for Single Channel Adapter

```c
//
// Target Channel Id
//
ExtScsiPassThruMode.AdapterId = 4;
//
// The channel does support nonblocking I/O
//
ExtScsiPassThruMode.Attributes = EFI_EXT_SCSI_PASS_THRU_ATTRIBUTES_PHYSICAL |
                                 EFI_EXT_SCSI_PASS_THRU_ATTRIBUTES_LOGICAL  |
                                 EFI_EXT_SCSI_PASS_THRU_ATTRIBUTES_NONBLOCKIO;
//
// Do not have any alignment requirement
//
ExtScsiPassThruMode.IoAlign = 0;
```

Example 214 shows how to set the SCSI _Mode_ structure on a multi-channel non-RAID adapter. The example fits for either channel in _Figure 23-Sample SCSI driver implementation on a multichannel adapter_.

###### Example 214-SCSI Pass Thru Mode Structure for Multi-Channel Adapter

```c
//
// Target Channel Id
//
ExtScsiPassThruMode.AdapterId = 2;
//
// The channel does not support nonblocking I/O
//
ExtScsiPassThruMode.Attributes = EFI_EXT_SCSI_PASS_THRU_ATTRIBUTES_PHYSICAL |
                                 EFI_EXT_SCSI_PASS_THRU_ATTRIBUTES_LOGICAL;
//
// Data must be alligned on a 4-byte boundary
//
ExtScsiPassThruMode.IoAlign = 2;
```

The next example shows how to set the corresponding _Mode_ structures for both
the physical and logical channel to be filled as shown below.

###### Example 215-SCSI Pass Thru Mode Structures for RAID SCSI adapter

```c
//
// ...... Physical Channel ......
//
ExtScsiPassThruMode.AdapterId  = 0;
ExtScsiPassThruMode.Attributes = EFI_EXT_SCSI_PASS_THRU_ATTRIBUTES_PHYSICAL |
                                 EFI_EXT_SCSI_PASS_THRU_ATTRIBUTES_NONBLOCKIO;
ExtScsiPassThruMode.IoAlign    = 0;

//
// ...... Logical Channel ......
//
ExtScsiPassThruMode.AdapterId  = 2;
ExtScsiPassThruMode.Attributes = EFI_EXT_SCSI_PASS_THRU_ATTRIBUTES_LOGICAL |
                                 EFI_EXT_SCSI_PASS_THRU_ATTRIBUTES_NONBLOCKIO;
ExtScsiPassThruMode.IoAlign    = 0;
```

The `EFI_EXT_SCSI_PASS_THRU_PROTOCOL.GetNextTarget()` and `EFI_EXT_SCSI_PASS_THRU_PROTOCOL.GetTargetLun()` functions provide the ability
to enumerate the SCSI targets attached to a SCSI channel. The SCSI host
controller driver may implement it by internally maintaining active device
flags. The SCSI host controller driver may use this flag and channel-specific
knowledge to determine what device is next, as well as what device is first.

The `EFI_EXT_SCSI_PASS_THRU_PROTOCOL.BuildDevicePath()` function facilitates
the construction of a SCSI device path. The Extended SCSI Pass Thru Protocol
may be used to abstract access to many different types of device, and as a
result the specific device path used to describe a SCSI target may vary. The
detailed SCSI target category can be identified only by the Extended SCSI Pass
Thru implementation, which is why this function is part of the Extended SCSI
Pass Thru Protocol.

The `EFI_EXT_SCSI_PASS_THRU_PROTOCOL.PassThru()` function is the most important
function when implementing Extended SCSI Pass Thru Protocol and it performs the
following:
* Initialize the internal register for command/data transfer.
* Put valid SCSI packets into hardware-specific memory or register locations.
* Start the transfer.
* Optionally wait for completion of the execution.

The better error handling mechanism in this function helps to develop a more
robust driver. Although most SCSI adapters support both blocking and
non-blocking data transfers, some may only support blocking transfers. In this
case, the SCSI driver may implement the blocking SCSI I/O that is required by
the _UEFI Specification_ using the polling mechanism. Polling can be based on a
timer interrupt or simply by polling the internal register. Do not return until
all I/O requests are completed or else an unhandled error is encountered.
