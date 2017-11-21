<!--- @file
  A.3.8 _BlockIo.c File_

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

### A.3.8 _BlockIo.c File_

###### Example A-20-Block I/O, Block I/O 2, and Storage Security Protocols implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include "<<DriverName>>.h"

///
/// Block I/O Media structure
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_BLOCK_IO_MEDIA g<<DriverName>>BlockIoMedia = {
  0,           // MediaId
  FALSE,       // RemovableMedia 
  FALSE,       // MediaPresent
  TRUE,        // LogicalPartition
  FALSE,       // ReadOnly
  FALSE,       // WriteCaching
  512,         // BlockSize
  0,           // IoAlign
  0,           // LastBlock
  0,           // LowestAlignedLba
  0,           // LogicalBlocksPerPhysicalBlock
  0            // OptimalTransferLengthGranularity
};

///
/// Block I/O Protocol instance
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_BLOCK_IO_PROTOCOL g<<DriverName>>BlockIo = {
  EFI_BLOCK_IO_PROTOCOL_REVISION3,              // Revision 
  &g<<DriverName>>BlockIoMedia,                 // Media
  (EFI_BLOCK_RESET) <<DriverName>>BlockIoReset, // Reset
  <<DriverName>>BlockIoReadBlocks,              // ReadBlocks
  <<DriverName>>BlockIoWriteBlocks,             // WriteBlocks
  <<DriverName>>BlockIoFlushBlocks              // FlushBlocks
};

///
/// Block I/O 2 Protocol instance
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_BLOCK_IO2_PROTOCOL g<<DriverName>>BlockIo2 = {
  &g<<DriverName>>BlockIoMedia,                 // Media
  <<DriverName>>BlockIoReset,                   // Reset
  <<DriverName>>BlockIoReadBlocksEx,            // ReadBlocks
  <<DriverName>>BlockIoWriteBlocksEx,           // WriteBlocks
  <<DriverName>>BlockIoFlushBlocksEx            // FlushBlocks
};

///
/// Storage Securtity Command Protocol instance
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_STORAGE_SECURITY_COMMAND_PROTOCOL g<<DriverName>>StorageSecurityCommand = {
  <<DriverName>>StorageSecurityCommandReceiveData,
  <<DriverName>>StorageSecurityCommandSendData
};

EFI_STATUS
EFIAPI
<<DriverName>>BlockIoReadBlocks (
  IN  EFI_BLOCK_IO_PROTOCOL                     *This,
  IN  UINT32                                    MediaId,
  IN EFI_LBA                                    Lba,
  IN  UINTN                                     BufferSize,
  OUT VOID                                      *Buffer
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>BlockIoWriteBlocks (
  IN EFI_BLOCK_IO_PROTOCOL                      *This,
  IN UINT32                                     MediaId,
  IN EFI_LBA                                    Lba,
  IN UINTN                                      BufferSize,
  IN VOID                                       *Buffer
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>BlockIoFlushBlocks (
  IN EFI_BLOCK_IO_PROTOCOL                     *This
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>BlockIoReset (
  IN EFI_BLOCK_IO2_PROTOCOL                    *This,
  IN BOOLEAN                                   ExtendedVerification
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>BlockIoReadBlocksEx (
  IN     EFI_BLOCK_IO2_PROTOCOL                *This,
  IN     UINT32                                MediaId,
  IN EFI_LBA                                   LBA,
  IN OUT EFI_BLOCK_IO2_TOKEN                   *Token,
  IN     UINTN                                 BufferSize,
  OUT    VOID                                  *Buffer
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>BlockIoWriteBlocksEx (
  IN     EFI_BLOCK_IO2_PROTOCOL                *This,
  IN     UINT32                                MediaId,
  IN EFI_LBA                                   LBA,
  IN OUT EFI_BLOCK_IO2_TOKEN                   *Token,
  IN     UINTN                                 BufferSize,
  IN     VOID                                  *Buffer
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>BlockIoFlushBlocksEx (
  IN     EFI_BLOCK_IO2_PROTOCOL                *This,
  IN OUT EFI_BLOCK_IO2_TOKEN                   *Token
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>StorageSecurityCommandReceiveData (
  IN  EFI_STORAGE_SECURITY_COMMAND_PROTOCOL    *This,
  IN  UINT32                                   MediaId,
  IN  UINT64                                   Timeout,
  IN  UINT8                                    SecurityProtocolId,
  IN  UINT16                                   SecurityProtocolSpecificData,
  IN  UINTN                                    PayloadBufferSize, 
  OUT VOID                                     *PayloadBuffer
  OUT UINTN                                    *PayloadTransferSize
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>StorageSecurityCommandSendData (
  IN EFI_STORAGE_SECURITY_COMMAND_PROTOCOL     *This,
  IN UINT32                                    MediaId,
  IN UINT64                                    Timeout,
  IN UINT8                                     SecurityProtocolId,
  IN UINT16                                    SecurityProtocolSpecificData,
  IN UINTN                                     PayloadBufferSize,
  IN VOID                                      *PayloadBuffer
  )
{
}
```
