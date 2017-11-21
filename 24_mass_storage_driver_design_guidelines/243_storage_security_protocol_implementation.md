<!--- @file
  24.3 Storage Security Protocol Implementation

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

## 24.3 Storage Security Protocol Implementation

The implementation of the Storage Security Protocol is only required if the
mass storage device supports the SPC-4 or ATA8-ACS security commands. The
implementation of the Storage Security Protocol is typically found in the file
Block.c. Appendix A contains a template for a BlockIo.c file for a UEFI Driver.
The list of tasks to implement the Storage Security Protocol is as follows:

* Add global variable for the `EFI_STORAGE_SECURITY_COMMAND_PROTOCOL` instance
  to `BlockIo.c`.

* Implement the Storage Security Command Protocol services in `BlockIo.c`.

This example shows the protocol interface structure for the optional Storage
Security Command Protocol for reference. It is composed of two services to send
and receive data.

###### Example 238-Storage Security Command Protocol

```c
typedef struct _EFI_STORAGE_SECURITY_COMMAND_PROTOCOL
  EFI_STORAGE_SECURITY_COMMAND_PROTOCOL;

///
/// The EFI_STORAGE_SECURITY_COMMAND_PROTOCOL is used to send security protocol
/// commands to a mass storage device. Two types of security protocol commands
/// are supported. SendData sends a command with data to a device. ReceiveData
/// sends a command that receives data and/or the result of one or more commands
/// sent by SendData.
///
/// The security protocol command formats supported shall be based on the
/// definition of the SECURITY PROTOCOL IN and SECURITY PROTOCOL OUT commands
/// defined in SPC-4 If the device uses the SCSI command set, no translation is
/// needed in the firmware and the firmware can package the parameters into a
/// SECURITY PROTOCOL IN or SECURITY PROTOCOL OUT command and send the command to
/// the device. If the device uses a non-SCSI command set, the firmware shall map
/// the command and data payload to the corresponding command and payload format
/// defined in the non-SCSI command set (for example, TRUSTED RECEIVE and TRUSTED
/// SEND in ATA8-ACS).
///
/// The firmware shall automatically add an EFI_STORAGE_SECURITY_COMMAND_PROTOCOL
/// for any storage devices detected during system boot that support SPC-4,
/// ATA8-ACS or their successors.
///
struct _EFI_STORAGE_SECURITY_COMMAND_PROTOCOL {
  EFI_STORAGE_SECURITY_RECEIVE_DATA ReceiveData;
  EFI_STORAGE_SECURITY_SEND_DATA SendData;
};

extern EFI_GUID gEfiStorageSecurityCommandProtocolGuid;
```

The EDK II has a complete implementation of the Storage Security Protocol for
ATA device in the `MdeModulePkg` in the directory
`MdeModulePkg/Bus/Ata/AtaBusDxe`. This can be used as a reference for
implementations of the Storage Security Protocol for mass storage devices on
other bus types.
