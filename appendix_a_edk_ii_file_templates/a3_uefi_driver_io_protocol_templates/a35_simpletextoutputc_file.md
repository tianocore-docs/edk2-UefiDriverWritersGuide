<!--- @file
  A.3.5 _SimpleTextOutput.c File_

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

### A.3.5 _SimpleTextOutput.c File_

###### Example A-17-Simple Text Output Protocol implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include "<<DriverName>>.h"

///
/// Simple Text Output Protocol Mode instance
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_SIMPLE_TEXT_OUTPUT_MODE g<<DriverName>>SimpleTextOutputMode = {
  0,                                               // MaxMode
  0,                                               // Mode
  EFI_TEXT_ATTR (EFI_WHITE, EFI_BACKGROUND_BLACK), // Attribute
  0,                                               // CursorColumn
  0,                                               // CursorRow
  TRUE                                             // CursorVisible
};

///
/// Simple Text Output Protocol instance
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL g<<DriverName>>SimpleTextOutput = {
  <<DriverName>>SimpleTextOutputReset,
  <<DriverName>>SimpleTextOutputOutputString, 
  <<DriverName>>SimpleTextOutputTestString,
  <<DriverName>>SimpleTextOutputQueryMode, 
  <<DriverName>>SimpleTextOutputSetMode,
  <<DriverName>>SimpleTextOutputSetAttribute,
  <<DriverName>>SimpleTextOutputClearScreen,
  <<DriverName>>SimpleTextOutputSetCursorPosition,
  <<DriverName>>SimpleTextOutputEnableCursor,
  &g<<DriverName>>SimpleTextOutputMode
};
  
EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextOutputReset (
  IN EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL    *This,
  IN BOOLEAN                            ExtendedVerification
  )
{
}
  
EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextOutputOutputString (
  IN EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL    *This,
  IN CHAR16                             *String
  )
{
}
  
EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextOutputTestString (
  IN EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL    *This,
  IN CHAR16                             *String
  )
{
}
  
EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextOutputQueryMode (
  IN  EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL   *This,
  IN  UINTN                             ModeNumber,
  OUT UINTN                             *Columns,
  OUT UINTN                             *Rows
  )
{
}
  
EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextOutputSetMode (
  IN EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL    *This,
  IN UINTN                              ModeNumber
  )
{
}
  
EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextOutputSetAttribute (
  IN EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL    *This,
  IN UINTN                              Attribute
  )
{
}
  
EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextOutputClearScreen (
  IN EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL    *This
  )
{
}
  
EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextOutputSetCursorPosition (
  IN EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL    *This,
  IN UINTN                              Column,
  IN UINTN                              Row
  )
{
}
  
EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextOutputEnableCursor (
  IN EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL    *This,
  IN BOOLEAN                            Visible
  )
{
}
```
