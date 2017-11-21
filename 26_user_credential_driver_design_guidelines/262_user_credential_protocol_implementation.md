<!--- @file
  26.2 User Credential Protocol Implementation

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

## 26.2 User Credential Protocol Implementation

The implementation of the User Credential Protocol is typically found in the
file `UserCredential.c`. Appendix A contains a template for a `UserCredential.c` file for a UEFI Driver. The list of tasks to implement the User Credential Protocol is as follows:

* Add global variable for the `EFI_USER_CREDENTIAL2_PROTOCOL` instance to
  `UserCredential.c`.

* Add implementations of the services produced by the User Credential Protocol
  to `UserCredential.c`.

* Implement HII forms for interacting with the user during the user identify
  process using a formset GUID of `EFI_USER_CREDENTIAL_PROTOCOL_GUID`. See
  _Chapter 12_ for details on HII forms.

* Implement HII Config Access Protocol to retrieve and save configuration
  information associated with the HII forms. See _Chapter 12_ for details on the HII Config Access Protocol. The implementation of the HII Config Access Protocol is typically found in the file HiiConfigAccess.c. Appendix A contains a template for a HiiConfigAccess.c file for a UEFI Driver.

The example below shows the protocol interface structure for the User
Credential Protocol for reference. This protocol is composed of two GUIDs, 11
services, and a capabilities value. These services are used by a User Identity
Manager to identify the current user of a platform.

###### Example 241-User Credential Protocol

```c
typedef struct _EFI_USER_CREDENTIAL2_PROTOCOL EFI_USER_CREDENTIAL2_PROTOCOL;

///
/// This protocol provides support for a single class of credentials
///
struct _EFI_USER_CREDENTIAL2_PROTOCOL {
  EFI_GUID                      Identifier;   ///< Uniquely identifies this
                                              ///< credential provider.
  EFI_GUID                      Type;         ///< Identifies this class of User
                                              ///<Credential Provider.
  EFI_CREDENTIAL2_ENROLL        Enroll;
  EFI_CREDENTIAL2_FORM          Form;
  EFI_CREDENTIAL2_TILE          Tile;
  EFI_CREDENTIAL2_TITLE         Title;
  EFI_CREDENTIAL2_USER          User;
  EFI_CREDENTIAL2_SELECT        Select;
  EFI_CREDENTIAL2_DESELECT      Deselect;
  EFI_CREDENTIAL2_DEFAULT       Default;
  EFI_CREDENTIAL2_GET_INFO      GetInfo;
  EFI_CREDENTIAL2_GET_NEXT_INFO GetNextInfo;
  EFI_CREDENTIAL_CAPABILITIES   Capabilities;
  EFI_CREDENTIAL2_DELETE        Delete;
};

extern EFI_GUID gEfiUserCredential2ProtocolGuid;
```
