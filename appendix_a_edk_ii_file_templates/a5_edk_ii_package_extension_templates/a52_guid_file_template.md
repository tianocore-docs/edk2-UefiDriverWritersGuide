<!--- @file
  A.5.2 _GUID File Template_

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

### A.5.2 _GUID File Template_

GUIDs and their associated data structures are declared just like protocols.
The only difference is that GUIDs are typically placed in a different
subdirectory of an EDK II package. The typical path to a protocol .h file is
`<<PackageName>>/Include/Guid/<<GuidName>>.h`. For example, all the GUIDs
defined in the UEFI Specification can be found in the EDK II MdePg in
/MdePkg/Include/Guid. When a new GUID is defined and added to an include
directory of an EDK II package, the GUID must also be added to the [Guids]
section of a package's .dec file. The .dec file is where the C variable name
for the GUID is declared and mapped to GUID value for the protocol. Defining a
new GUID is not commonly required when implementing a new UEFI Driver. If a
UEFI Driver implementation does require a new GUID definition, then the new
GUID is usually added to the same EDK II package that contains the UEFI Driver
implementation.

The following example shows a template for adding a new GUID to the [Guids]
section of an EDK II package .dec file. Following that, Example A-29 shows the
template for the .h files for GUIDs placed in the include directory of an EDK
II package.

###### Example A-28-Add GUID to an EDK II package

```ini
[Guids]
  Include/Guid/<<GuidName>>.h
  gEfi<<GuidName>>Guid = <<GUID_STRUCT>>
```

###### Example A-29-GUID include file template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#ifndef __<<GUID_NAME>>_H__
#define __<<GUID_NAME>>_H__

#define EFI_<<GUID_NAME>>_GUID \
  <<GUID_STRUCT>>

///
/// GUID specific defines
///
///
/// GUID specific structures
///
typedef struct {
  //
  // Place GUID specific data fields here
  //
} EFI_<<GUID_NAME>>_GUID;

extern EFI_GUID gEfi <<GuidName>>Guid;

#endif
```
