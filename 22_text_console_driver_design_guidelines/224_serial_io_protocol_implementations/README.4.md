<!--- @file
  22.4 Serial I/O Protocol Implementations

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

## 22.4 Serial I/O Protocol Implementations

The implementation of the Serial I/O Protocol is typically found in the file
SerialIo.c. Appendix A contains a template for a SerialIo.c file for a UEFI
Driver. The list of tasks to implement the Serial I/O Protocol is as follows:
* Add global variable for the `EFI_SERIAL_IO_PROTOCOL` instance to `SerialIo.c`.
* Add global variable for the `EFI_SERIAL_IO_MODE` structure to `SerialIo.c`.
* Implement the Serial I/O Protocol services in `SerialIo.c`.
* Create a child handle with the Serial I/O Protocol and a Device Path Protocol.

The Device Path Protocol chapter of the _UEFI Specification_ defines a UART
Device Path Node that must be used in the Device Path Protocol for any device
that supports the Serial I/O Protocol.

This example shows the protocol interface structure for the Serial I/O Protocol
for reference. This protocol is composed of six services, a _Revision_ value,
and pointer to a _Mode_ structure.

###### Example 230-Simple Text Output Protocol

```c
typedef struct _EFI_SERIAL_IO_PROTOCOL EFI_SERIAL_IO_PROTOCOL;

///
/// The Serial I/O protocol is used to communicate with UART-style serial devices.
/// These can be standard UART serial ports in PC-AT systems, serial ports attached
/// to a USB interface, or potentially any character-based I/O device.
///
struct _EFI_SERIAL_IO_PROTOCOL {
  ///
  /// The revision to which the EFI_SERIAL_IO_PROTOCOL adheres. All future
  /// revisions must be backwards compatible. If a future version is not backwards
  /// compatible, it is not the same GUID.
  ///
  UINT32 Revision;
  EFI_SERIAL_RESET             Reset;
  EFI_SERIAL_SET_ATTRIBUTES    SetAttributes;
  EFI_SERIAL_SET_CONTROL_BITS  SetControl;
  EFI_SERIAL_GET_CONTROL_BITS  GetControl;
  EFI_SERIAL_WRITE             Write;
  EFI_SERIAL_READ              Read;
  ///
  /// Pointer to SERIAL_IO_MODE data.
  ///
  EFI_SERIAL_IO_MODE           *Mode;
};
```
**********
**Note:** Mode must be updated each time that `SetControl()' or
'SetAttributes()' is called. This allows the consumers of the Serial I/O
Protocol to retrieve the current state of the Serial I/O device.
**********
