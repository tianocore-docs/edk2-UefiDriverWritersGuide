<!--- @file
  Appendix A EDK II File Templates

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

# Appendix A EDK II File Templates

This discussion contains templates and guidelines for creating files for
protocols, GUIDs, EDK II Library Classes, and UEFI drivers in EDK II packages.
The naming conventions for the driver entry point and the functions exported by
a driver that are presented here guarantee that a unique name is produced for
every function, which aides in call stack analysis when root-causing driver
issues. The Doxygen style function header comment blocks have been removed the
file templates shown in this appendix to highlight the source code elements
required to build a UEFI Driver. The function headers comments blocks can be
added by coping them from the EDK II MdePkg protocol include files located in
the MdePkg/Include/Protocol/ directory.

The following expressions are used throughout this chapter to show where
protocol names, GUID names, function names, and driver names should be
substituted in a file template: 

`<<PackageName>>`

Represents the name of a package follows the function or variable naming
convention, which capitalizes only the first letter of each word (e.g.,
`MdePkg`).

`<<BriefDescription>>`

One line brief description of a file or library or module.

`<<DetailedDescription>>`

Paragraph that is a detailed description of a file or library or module.

`<<Copyright>>`

One or more copyright declarations for a file or library or module.

`<<License>>`

One or more licenses for a file or library or module.

`<<ProtocolName>>`

Represents the name of a protocol that follows the function or variable naming
convention, which capitalizes only the first letter of each word (e.g.,
`DiskIo`).

`<<PROTOCOL_NAME>>`

Represents the name of a protocol that follows the data structure naming
convention, which capitalizes all the letters and separates each word with an
underscore '_' (e.g., `DISK_IO`).

`<<GUID_STRUCT>>`

Represents the GUID value in the format of a C data structure. New GUID values
can be generated using the `GUIDGEN` utility shipped with Microsoft*
compilers, or the `uuidgen` command under Linux - e.g. `{ 0x9e34954, 0x6c5, 0x4e1a, 
{ 0xb7,0xeb, 0x5d, 0x5c, 0x9, 0xca, 0x6d, 0xaf } }`.

`<<GUID_REGISTRY_FORMAT>>`

Represents the GUID value in Registry Format. New GUID values can be generated

using the `GUIDGEN` utility shipped with Microsoft* compilers, or the
`uuidgen` command under Linux. (e.g., `1E9CD853-7A32-49e0-8140-145CD35C6632`)

`<<DriverVersion>>`

A 32-bit value representation for the version of the UEFI Driver used to fill
in the `Version` field of the Driver Binding Protocol. (e.g., `0x00000010`).

`<<DriverVersionString>>`

A text string representation for the version of the UEFI Driver. (e.g., 1.7).

`<<FunctionNameN>>`

Represents the _nth_ name of the protocol member functions that follow the
function or variable naming convention, which capitalizes only the first letter
of each word (e.g., `ReadDisk`). 

`<<FUNCTION_NAMEn>>`

Represents the _nth_ name of the protocol member functions that follows the
data structure naming convention, which capitalizes all the letters and
separates each word with an underscore '_' (e.g., `READ_DISK`).

`<<GuidName>>`

Represents the name of a GUID that follows the function or variable naming
convention, which capitalizes only the first letter of each word (e.g.,
`GlobalVariable`).

`<<GUID_NAME>>`

Represents the name of a GUID that follows the data structure naming
convention, which capitalizes all the letters and separates each word with an
underscore '_' (e.g., `GLOBAL_VARIABLE`).

`<<DriverName>>`

Represents the name of a driver that follows the function or variable naming
convention, which capitalizes only the first letter of each word (e.g.,
`Ps2Keyboard`). 

`<<DRIVER_NAME>>`

Represents the name of a driver that follows the data structure naming
convention, which capitalizes all the letters and separates each word with an
underscore '_' (e.g., `PS2_KEYBOARD`). 

`<<DriverVersion>>`

Value that represents the version of the driver. Values from 0x0-0x0f and
0xFFFFFFF0-0xFFFFFFFF are reserved for UEFI drivers that are written by OEMs
for integrated devices. Values from 0x10-0xFFFFFFEF are reserved for UEFI
drivers that are written by IHVs. 

`<<Iso639SupportedLanguages>>`

A null terminated ASCII string of one or more 3 character ISO 639-2 language
code with no separator character. (e.g. "eng" for English, "engfra" for English
and French).

`<<Rfc4646SupportedLanguages>>`

A null terminated ASCII string of one or more RFC 4646 language codes separated
by semicolons (';') (e.g. "en" for English, "en-US;fr" for U.S. English and
French). 

`<<UEFI_SYSTEM_TABLE_REVISON>>`

The 32-bit revision of the _UEFI Specification_ that the UEFI Driver requires
to run correctly. Usually, one of the define names from `<Uefi.h>` is used, which
includes the following:

```
EFI_2_31_SYSTEM_TABLE_REVISION

EFI_2_30_SYSTEM_TABLE_REVISION

EFI_2_20_SYSTEM_TABLE_REVISION

EFI_2_10_SYSTEM_TABLE_REVISION

EFI_2_00_SYSTEM_TABLE_REVISION

EFI_1_10_SYSTEM_TABLE_REVISION

EFI_1_02_SYSTEM_TABLE_REVISION
```

`<<ProtocolNameCn>>`

Represents the _nth_ name of a protocol that is consumed by a UEFI driver and
follows the function or variable naming convention, which capitalizes only the
first letter of each word (e.g., `DiskIo`).

`<<PROTOCOL_NAME_CN>>`

Represents the _nth_ name of a protocol that is consumed by a UEFI driver and
follows the data structure naming convention, which capitalizes all the letters
and separates each word with an underscore '_' (e.g., `DISK_IO`).

`<<ProtocolNamePm>>`

Represents the _mth_ name of a protocol produced by a UEFI driver that follows
the function or variable naming convention which capitalizes only the first
letter of each word (e.g., `DiskIo`).

`<<PROTOCOL_NAME_PM>>`

Represents the _mth_ name of a protocol that is produced by a UEFI driver and
follows the data structure naming convention, which capitalizes all the letters
and separates each word with an underscore '_' (e.g., `DISK_IO`).

`<<UsbSpecificationMajorRevision>>`

Denotes the major revision of the _USB Specification_ that a USB host
controller driver follows (e.g. 1 for the _USB 1.1 Specification)_.

`<<UsbSpecificationMinorRevision>>`

Denotes the minor revision of that _USB Specification_ that a USB host
controller driver follows (e.g. 0 for the _USB 2.0 Specification)_.
