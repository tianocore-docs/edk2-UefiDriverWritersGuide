<!--- @file
  24.2 Block I/O Protocol Implementations

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

## 24.2 Block I/O Protocol Implementations

The implementation of the Block I/O Protocols is typically found in the file
Block.c. Appendix A contains a template for a BlockIo.c file for a UEFI Driver.
The list of tasks to implement the Block I/O Protocols is as follows:

* Add global variable for the `EFI_BLOCK_IO_PROTOCOL` instance to `BlockIo.c`.

* Add global variable for the `EFI_BLOCK_IO2_PROTOCOL` instance to `BlockIo.c`.

* Add global variable for the `EFI_BLOCK_IO_MODE` structure to `BlockIo.c`.

* Implement the Block I/O Protocol and Block I/O 2 Protocol services in
  `BlockIo.c`.

Example 236, below, shows the protocol interface structure for the Block I/O
Protocol and the following Example 237 shows the protocol interface structure
for the Block I/O 2 Protocol for reference. These two protocols are very
similar and are both composed of four services and a pointer to a structure
that provides detailed information on the currently mounted media. The main
difference between these two protocols is that the Block I/O 2 Protocol
supports non-blocking operations.

###### Example 236-Block I/O Protocol

```c
typedef struct _EFI_BLOCK_IO_PROTOCOL EFI_BLOCK_IO_PROTOCOL;

///
/// This protocol provides control over block devices.
///
struct _EFI_BLOCK_IO_PROTOCOL {
  ///
  /// The revision to which the block IO interface adheres. All future
  /// revisions must be backwards compatible. If a future version is not
  /// back wards compatible, it is not the same GUID.
  ///
  UINT64 Revision;
  ///
  /// Pointer to the EFI_BLOCK_IO_MEDIA data for this device.
  ///
  EFI_BLOCK_IO_MEDIA *Media;
  
  EFI_BLOCK_RESET Reset;
  EFI_BLOCK_READ ReadBlocks;
  EFI_BLOCK_WRITE WriteBlocks;
  EFI_BLOCK_FLUSH FlushBlocks;
  
};

extern EFI_GUID gEfiBlockIoProtocolGuid;
```

**********
**Note:** Media must be updated each time that media in the mass storage device is inserted or removed. This allows the consumers of the Block I/O
Protocol to retrieve the state of the currently mounted media.
**********

###### Example 237-Block I/O 2 Protocol

```c
typedef struct _EFI_BLOCK_IO2_PROTOCOL EFI_BLOCK_IO2_PROTOCOL;

///
/// The Block I/O2 protocol defines an extension to the Block I/O protocol which
/// enables the ability to read and write data at a block level in a non-blocking
// manner.
///
struct _EFI_BLOCK_IO2_PROTOCOL {
  ///
  /// A pointer to the EFI_BLOCK_IO_MEDIA data for this device.
  /// Type EFI_BLOCK_IO_MEDIA is defined in BlockIo.h.
  ///
  EFI_BLOCK_IO_MEDIA *Media;
  
  EFI_BLOCK_RESET_EX Reset;
  EFI_BLOCK_READ_EX ReadBlocksEx;
  EFI_BLOCK_WRITE_EX WriteBlocksEx;
  EFI_BLOCK_FLUSH_EX FlushBlocksEx;
};

extern EFI_GUID gEfiBlockIo2ProtocolGuid;
```

**********
**Note:** Media must be updated each time that that media in the mass storage device is inserted or removed. This allows the consumers of the Block I/O 2
Protocol to retrieve the state of the currently mounted media.
**********
