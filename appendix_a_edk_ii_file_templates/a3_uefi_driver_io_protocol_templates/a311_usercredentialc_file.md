<!--- @file
  A.3.11 _UserCredential.c File_

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

### A.3.11 _UserCredential.c File_

###### Example A-23-User Credential Protocol implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include "<<DriverName>>.h"

///
/// User Credential Protocol instance
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_USER_CREDENTIAL2_PROTOCOL g<<DriverName>>UserCredential = {
  { 0x0, 0x0, 0x0, {0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0}}, // Identifier
  { 0x0, 0x0, 0x0, {0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0}}, // Type
  <<DriverName>>UserCredentialEnroll,                         // Enroll
  <<DriverName>>UserCredentialForm,                           // Form
  <<DriverName>>UserCredentialTile,                           // Tile
  <<DriverName>>UserCredentialTitle,                          // Title
  <<DriverName>>UserCredentialUser,                           // User
  <<DriverName>>UserCredentialSelect,                         // Select
  <<DriverName>>UserCredentialDeselect,                       // Deselect
  <<DriverName>>UserCredentialDefault,                        // Default
  <<DriverName>>UserCredentialGetInfo,                        // GetInfo
  <<DriverName>>UserCredentialGetNextInfo,                    // GetNextInfo
  0,                                                          // Capabilities
  <<DriverName>>UserCredentialDelete                          // Delete 
};
  
EFI_STATUS
EFIAPI
<<DriverName>>UserCredentialEnroll (
  IN CONST EFI_USER_CREDENTIAL2_PROTOCOL                      *This,
  IN       EFI_USER_PROFILE_HANDLE                            User
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>UserCredentialForm (
  IN CONST EFI_USER_CREDENTIAL2_PROTOCOL                      *This,
  OUT      EFI_HII_HANDLE                                     *Hii,
  OUT      EFI_GUID                                           *FormSetId,
  OUT      EFI_FORM_ID                                        *FormId
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>UserCredentialTile (
  IN CONST EFI_USER_CREDENTIAL2_PROTOCOL                      *This,
  IN OUT   UINTN                                              *Width,
  IN OUT   UINTN                                              *Height,
  OUT      EFI_HII_HANDLE                                     *Hii,
  OUT      EFI_IMAGE_ID                                       *Image
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>UserCredentialTitle (
  IN CONST EFI_USER_CREDENTIAL2_PROTOCOL                      *This,
  OUT      EFI_HII_HANDLE                                     *Hii,
  OUT      EFI_STRING_ID                                      *String
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>UserCredentialUser (
  IN CONST EFI_USER_CREDENTIAL2_PROTOCOL                      *This,
  IN       EFI_USER_PROFILE_HANDLE                            User,
  OUT      EFI_USER_INFO_IDENTIFIER                           *Identifier
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>UserCredentialSelect (
  IN CONST EFI_USER_CREDENTIAL2_PROTOCOL                      *This,
  OUT      EFI_CREDENTIAL_LOGON_FLAGS                         *AutoLogon
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>UserCredentialDeselect (
  IN CONST EFI_USER_CREDENTIAL2_PROTOCOL                      *This
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>UserCredentialDefault (
  IN CONST EFI_USER_CREDENTIAL2_PROTOCOL                      *This,
  OUT      EFI_CREDENTIAL_LOGON_FLAGS                         *AutoLogon
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>UserCredentialGetInfo (
  IN CONST EFI_USER_CREDENTIAL2_PROTOCOL                      *This,
  IN       EFI_USER_INFO_HANDLE                               UserInfo,
  OUT      EFI_USER_INFO                                      *Info,
  IN OUT   UINTN                                              *InfoSize
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>UserCredentialGetNextInfo (
  IN CONST EFI_USER_CREDENTIAL2_PROTOCOL                      *This,
  IN OUT EFI_USER_INFO_HANDLE                                 *UserInfo
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>UserCredentialDelete (
  IN CONST EFI_USER_CREDENTIAL2_PROTOCOL                      *This,
  IN EFI_USER_PROFILE_HANDLE                                  User
  )
{
}
```
