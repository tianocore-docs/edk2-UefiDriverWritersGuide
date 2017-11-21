<!--- @file
  A.3.7 _GraphicsOutput.c File_

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

### A.3.7 _GraphicsOutput.c File_

###### Example A-19-Graphics Output Protocol implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include "<<DriverName>>.h"

///
/// Graphics Output Protocol Mode structure
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_GRAPHICS_OUTPUT_PROTOCOL_MODE g<<DriverName>>GraphicsOutputMode = {
  0,                  // MaxMode
  0,                  // Mode
  NULL,               // Info
  0,                  // SizeOfInfo
  0,                  // FrameBufferBase
  0                   // FrameBufferSize
};

///
/// Graphics Output Protocol instance
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_GRAPHICS_OUTPUT_PROTOCOL g<<DriverName>>GraphicsOutput = {
  <<DriverName>>GraphicsOutputQueryMode,
  <<DriverName>>GraphicsOutputSetMode, 
  <<DriverName>>GraphicsOutputBlt,
  &g<<DriverName>>GraphicsOutputMode
};

EFI_STATUS
EFIAPI
  <<DriverName>>GraphicsOutputQueryMode (
  IN  EFI_GRAPHICS_OUTPUT_PROTOCOL          *This,
  IN  UINT32                                ModeNumber,
  OUT UINTN                                 *SizeOfInfo,
  OUT EFI_GRAPHICS_OUTPUT_MODE_INFORMATION  **Info
  )
{
}

EFI_STATUS
EFIAPI
    <<DriverName>>GraphicsOutputSetMode (
  IN EFI_GRAPHICS_OUTPUT_PROTOCOL           *This,
  IN UINT32                                 ModeNumber
  )
{
}

EFI_STATUS
EFIAPI
    <<DriverName>>GraphicsOutputBlt (
  IN EFI_GRAPHICS_OUTPUT_PROTOCOL           *This,
  IN EFI_GRAPHICS_OUTPUT_BLT_PIXEL          *BltBuffer,    OPTIONAL
  IN EFI_GRAPHICS_OUTPUT_BLT_OPERATION      BltOperation,
  IN UINTN                                  SourceX,
  IN UINTN                                  SourceY,
  IN UINTN                                  DestinationX,
  IN UINTN                                  DestinationY,
  IN UINTN                                  Width,
  IN UINTN                                  Height,
  IN UINTN                                  Delta          OPTIONAL
  )
{
}
```
