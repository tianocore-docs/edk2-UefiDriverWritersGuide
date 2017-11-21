<!--- @file
  A.3.6 _SerialIo.c File_

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

### A.3.6 _SerialIo.c File_

###### Example A-18-Serial I/O Protocol implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include "<<DriverName>>.h"

///
/// Serial I/O Protocol Mode instance
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_SERIAL_IO_MODE g<<DriverName>>SerialIoMode = {
  0x00000000,             // ControlMask
  0,                      // Timeout
  0,                      // BaudRate
  0,                      // ReceiveF ifoDepth
  0,                      // DataBits
  DefaultParity,          // Parity
  DefaultStopBits         // StopBits
};

///
/// Serial I/O Protocol instance
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_SERIAL_IO_PROTOCOL g<<DriverName>>SerialIo = {
  EFI_SERIAL_IO_PROTOCOL_REVISION,
  <<DriverName>>SerialIoReset,
  <<DriverName>>SerialIoSetAttributes, 
  <<DriverName>>SerialIoSetControl,
  <<DriverName>>SerialIoGetControl,
  <<DriverName>>SerialIoWrite,
  <<DriverName>>SerialIoRead,
  &g<<DriverName>>SerialIoMode
};

EFI_STATUS
EFIAPI
<<DriverName>>SerialIoReset (
  IN EFI_SERIAL_IO_PROTOCOL      *This
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SerialIoSetAttributes (
  IN EFI_SERIAL_IO_PROTOCOL      *This,
  IN UINT64                      BaudRate,
  IN UINT32                      ReceiveFifoDepth,
  IN UINT32                      Timeout,
  IN EFI_PARITY_TYPE             Parity,
  IN UINT8                       DataBits,
  IN EFI_STOP_BITS_TYPE          StopBits
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SerialIoSetControl (
  IN EFI_SERIAL_IO_PROTOCOL      *This,
  IN UINT32                      Control
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SerialIoGetControl (
  IN EFI_SERIAL_IO_PROTOCOL      *This,
  OUT UINT32                     *Control
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SerialIoWrite (
  IN EFI_SERIAL_IO_PROTOCOL      *This,
  IN OUT UINTN                   *BufferSize,
  IN VOID                        *Buffer
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SerialIoRead (
  IN EFI_SERIAL_IO_PROTOCOL      *This,
  IN OUT UINTN                   *BufferSize,
  OUT VOID                       *Buffer
  )
{
}
```
