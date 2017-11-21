<!--- @file
  A.5.1 _Protocol File Template_

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

### A.5.1 Protocol File Template

The .h files for protocols are placed in the include directories of EDK II
packages. The typical path to a protocol .h file is
`<<PackageName>>/Include/Protocol/<<ProtocolName>>.h`. For example, all the
protocols defined in the UEFI Specification can be found in the EDK II `MdePkg`
in `/MdePkg/Include/Protocol`. When a new protocol is defined and added to an
include directory of an EDK II package, the protocol must also be added to the
`[Protocols]` section of a package's .dec file. The .dec file is where the C
variable name for the protocol is declared and mapped to GUID value for the
protocol. Defining a new protocol is not commonly required when implementing a
new UEFI Driver. If a UEFI Driver implementation does require a new protocol
definition, then the new protocol is usually added to the same EDK II package
that contains the UEFI Driver implementation.

The example below shows a template for adding a new protocol to the
`[Protocols]` section of an EDK II package .dec file. Example A-26 shows the
template for the .h files for protocols placed in the include directory of an
EDK II package.

###### Example A-26-Add protocol to an EDK II package

```ini
[Protocols]
  Include/Protocol/<<ProtocolName>>.h
  gEfi<<ProtocolName>>ProtocolGuid = <<GUID_STRUCT>>
```

###### Example A-27-Protocol include file template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#ifndef __<<PROTOCOL_NAME>>_H__
#define __<<PROTOCOL_NAME>>_H__

#define EFI_<<PROTOCOL_NAME>>_PROTOCOL_GUID \
  <<GUID_STRUCT>>
  
///
/// Forward declaration
///
typedef struct _EFI_<<PROTOCOL_NAME>>_PROTOCOL EFI_<<PROTOCOL_NAME>>_PROTOCOL;

///
/// Function prototypes
///
typedef
EFI_STATUS
(EFIAPI * EFI_<<PROTOCOL_NAME>>_<<FUNCTION_NAME_1>>)(
  IN EFI_<<PROTOCOL_NAME>>_PROTOCOL             *This
  //
  // Place additional function arguments here
  //
  );
  
typedef EFI_STATUS
(EFIAPI *EFI_<<PROTOCOL_NAME>>_<<FUNCTION_NAME_2>>)(
  IN EFI_<<PROTOCOL_NAME>>_PROTOCOL              *This
  //
  // Place additional function arguments here
  //
  );

typedef EFI_STATUS
(EFIAPI *EFI_<<PROTOCOL_NAME>>_<<FUNCTION_NAME_N>>)(
  IN EFI_<<PROTOCOL_NAME>>_PROTOCOL              *This
  //
  // Place additional function arguments here
  //
  );
  
///
/// Protocol structure
///
typedef struct_EFI_<<PROTOCOL_NAME>>_PROTOCOL {
  EFI_<<PROTOCOL_NAME>>_<<FUNCTION_NAME_1>>  <<FunctionName1>>;
  EFI_<<PROTOCOL_NAME>>_<<FUNCTION_NAME_2>>  <<FunctionName2>>;
  // . . .
  EFI_<<PROTOCOL_NAME>>_<<FUNCTION_NAME_N>>  <<FunctionNameN>>;
  //
  // Place protocol data fields here
  //
}

extern EFI_GUID gEfi <<ProtocolName>>ProtocolGuid;

#endif
```
