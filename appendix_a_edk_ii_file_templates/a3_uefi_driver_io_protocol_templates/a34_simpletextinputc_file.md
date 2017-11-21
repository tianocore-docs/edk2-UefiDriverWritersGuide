<!--- @file
  A.3.4 _SimpleTextInput.c File_

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

### A.3.4 _SimpleTextInput.c File_

###### Example A-16-Simple Text Input Protocols implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include "<<DriverName>>.h"

///
/// Simple Text Input Ex Protocol instance
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL g<<DriverName>>SimpleTextInputEx = {
  <<DriverName>>SimpleTextInputReset,
  <<DriverName>>SimpleTextInputReadKeyStrokeEx,
  NULL,
  <<DriverName>>SimpleTextInputSetState,
  <<DriverName>>SimpleTextInputRegisterKeyNotify,
  <<DriverName>>SimpleTextInputUnregisterKeyNotify
};

///
/// Simple Text Input Protocol instance
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_SIMPLE_TEXT_INPUT_PROTOCOL g<<DriverName>>SimpleTextInput = {
  (EFI_INPUT_RESET) <<DriverName>>SimpleTextInputReset,
  <<DriverName>>SimpleTextInputReadKeyStroke,
  NULL
};

EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextInputReset (
  IN EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL   *This,
  IN BOOLEAN                             ExtendedVerification
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextInputReadKeyStrokeEx (
  IN  EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL  *This,
  OUT EFI_KEY_DATA                       *KeyData
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextInputSetState (
  IN EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL   *This,
  IN EFI_KEY_TOGGLE_STATE                *KeyToggleState
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextInputRegisterKeyNotify (
  IN  EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL   *This,
  IN  EFI_KEY_DATA                        *KeyData,
  IN  EFI_KEY_NOTIFY_FUNCTION             KeyNotificationFunction,
  OUT EFI_HANDLE                          *NotifyHandle
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextInputUnregisterKeyNotify (
  IN EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL    *This,
  IN EFI_HANDLE                           NotificationHandle
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleTextInputReadKeyStroke (
  IN  EFI_SIMPLE_TEXT_INPUT_PROTOCOL      *This,
  OUT EFI_INPUT_KEY                       *Key
  )
{
}
```
