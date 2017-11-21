<!--- @file
  3.6 Protocols and handles

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

## 3.6 Protocols and handles

The extensible nature of UEFI is built, to a large degree, around protocols.
Protocols serve to enable communication between separately built modules,
including drivers.

Drivers create protocols consisting of two parts. The body of a protocol is a
C-style data structure known as a protocol interface structure, or just
"interface". The interface typically contains an associated set of function
pointers and data structures.

Every protocol has a GUID associated with it. The GUID serves as the name for
the protocol. The GUID also indicates the organization of the data structure
associated with the GUID. Note that the GUID is not part of the data structure
itself.

The example below shows a portion of the Component Named 2 Protocol definition
from the UEFI Driver Model chapter of the _UEFI Specification_. Notice that the
protocol data structure contains two functions and one data field.

###### Example 2-Protocol structure in EDK II

```c
///
/// Global ID for the Component Name Protocol
///
#define EFI_COMPONENT_NAME2_PROTOCOL_GUID \
  {0x6a7a5cff, 0xe8d9, 0x4f70, { 0xba, 0xda, 0x75, 0xab, 0x30, 0x25, 0xce, 0x14 } }

typedef struct _EFI_COMPONENT_NAME2_PROTOCOL EFI_COMPONENT_NAME2_PROTOCOL;

///
/// This protocol is used to retrieve user readable names of drivers
/// and controllers managed by UEFI Drivers.
///
struct _EFI_COMPONENT_NAME2_PROTOCOL {
  EFI_COMPONENT_NAME2_GET_DRIVER_NAME      GetDriverName;
  EFI_COMPONENT_NAME2_GET_CONTROLLER_NAME  GetControllerName;

  ///
  /// A Null-terminated ASCII string array that contains one or more
  /// supported language codes. This is the list of language codes that
  /// this protocol supports. The number of languages supported by a
  /// driver is up to the driver writer. SupportedLanguages is
  /// specified in RFC 4646 format.
  ///
  CHAR8 *SupportedLanguages;
};
```

Protocols are gathered into a single database. The database is not
"flat."Instead, it allows protocols to be grouped together. Each group is known
as a handle, and the handle is also the data type that refers to the group. The
database is thus known as the handle database. Handles are allocated
dynamically. Protocols are not required to be unique in the system, but they
must be unique on a handle. In other words, a handle may not be associated with
two protocols that have the same GUID.
