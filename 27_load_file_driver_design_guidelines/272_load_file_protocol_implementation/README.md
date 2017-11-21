<!--- @file
  27.2 Load File Protocol Implementation

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

## 27.2 Load File Protocol Implementation

The implementation of the Load File Protocol is typically found in the file
`LoadFile.c`. Appendix A contains a template for a `LoadFile.c` file for a UEFI
Driver. The list of tasks to implement the Load File Protocol is as follows:

* Add global variable for the `EFI_LOAD_FILE_PROTOCOL` instance to `LoadFile.c`.

* Implement the `LoadFile()` service in `LoadFile.c`.

The example below shows the protocol interface structure for the Load File
Protocol for reference. This protocol is composed of a single service called
`LoadFile()`. This service is typically used by a UEFI Boot Manager to boot a
UEFI OS Loader or other UEFI Application from a device that does not directly
or indirectly support the Simple File System Protocol.

###### Example 242-Load File Protocol

```c
typedef struct _EFI_LOAD_FILE_PROTOCOL EFI_LOAD_FILE_PROTOCOL;

struct _EFI_LOAD_FILE_PROTOCOL {
  EFI_LOAD_FILE LoadFile;
};

extern EFI_GUID gEfiLoadFileProtocolGuid;
```
