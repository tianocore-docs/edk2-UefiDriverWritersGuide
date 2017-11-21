<!--- @file
  A.2.3 _HiiConfigAccess.c File_

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

### A.2.3 _HiiConfigAccess.c File_

###### Example A-8-Driver Health Protocol implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include "<<DriverName>>.h"

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_HII_CONFIG_ACCESS_PROTOCOL g<<DriverName>>HiiConfigAccess = {
  <<DriverName>>HiiConfigAccessExtractConfig,
  <<DriverName>>HiiConfigAccessRouteConfig,
  <<DriverName>>HiiConfigAccessCallback
};

EFI_STATUS
EFIAPI
<<DriverName>>HiiConfigAccessExtractConfig (
  IN  CONST EFI_HII_CONFIG_ACCESS_PROTOCOL    *This,
  IN  CONST EFI_STRING                        Request,
  OUT EFI_STRING                              *Progress,
  OUT EFI_STRING                              *Results
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>HiiConfigAccessRouteConfig (
  IN  CONST EFI_HII_CONFIG_ACCESS_PROTOCOL    *This,
  IN  CONST EFI_STRING                        Configuration,
  OUT EFI_STRING                              *Progress
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>HiiConfigAccessCallback (
  IN     CONST EFI_HII_CONFIG_ACCESS_PROTOCOL  *This,
  IN     EFI_BROWSER_ACTION                    Action,
  IN     EFI_QUESTION_ID                       QuestionId,
  IN     UINT8                                 Type,
  IN OUT EFI_IFR_TYPE_VALUE                    *Value,
  OUT    EFI_BROWSER_ACTION_REQUEST            *ActionRequest
  )
{
}
```
