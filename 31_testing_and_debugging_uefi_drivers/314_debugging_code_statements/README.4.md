<!--- @file
  31.4 Debugging code statements

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

## 31.4 Debugging code statements

A UEFI Driver may be implemented to support both a debug (check) build and a
production build. The debug build includes code that helps debug a UEFI Driver
that is not included in normal production builds. UEFI Driver sources are
typically implemented with all the debug build statements included. The DSC
file used to build the UEFI Driver with the EDK II build tools contains
statements to select a debug build or a production build with no source changes
to the UEFI Driver.

The EDK II library class called `DebugLib` provides macros that can be used to
insert debug code into a checked build. This debug code can greatly reduce the
amount of time it takes to root cause a bug. These macros are typically enabled
only for debug builds and disabled in production builds so as to not take up
any executable space. The macros available through the DebugLib include:

* `ASSERT (`_Expression_`)`

* `ASSERT_EFI_ERROR (`_Status_`)`

* `ASSERT_PROTOCOL_ALREADY_INSTALLED (`_Handle, Guid_`)`

* `DEBUG ((`_ErrorLevel, Format,. ._`))`

* `DEBUG_CODE_BEGIN ()`

* `DEBUG_CODE_END ()`

* `DEBUG_CODE (`_Expression_`)`

* `DEBUG_CLEAR_MEMORY (`_Address, Length_`)`

* `CR (`_Record, TYPE, Field, Signature_`)`

These macros are described in details in the `MdePkg` documentation available
from [_http://www.tianocore.org_.](http://www.tianocore.org/) The _ErrorLevel_
parameter passed into the `DEBUG()` macro allows a UEFI driver to assign a
different error level to each debug message, which allows debug messages to be
filtered. The DSC files required to build a UEFI Driver can be used to set the
_ErrorLevel_ filter mask. The UEFI Shell also supports the `Err` command that
allows the user to set the error level filter mask.

**********
**TIP:** Use a serial port as a standard error device during debug. This a
terminal emulator to be used to log debug messages to a file.
**********

The table below contains the list of error levels that are supported in the
UEFI Shell. Other levels are usable, but not defined for a specific area.

<div style="page-break-after: always;"></div>

###### Table 46-Error levels

| **Mnemonic**     | **Value**  | **Description**                                                                  |
| ---------------- | ---------- | -------------------------------------------------------------------------------- |
| `DEBUG_INIT`     | 0x00000001 | Initialization                                                                   |
| `DEBUG_WARN`     | 0x00000002 | Warnings                                                                         |
| `DEBUG_INFO`     | 0x00000040 | Information messages                                                             |
| `DEBUG_ERROR`    | 0x80000000 | Error messages.                                                                  |
| `DEBUG_FS`       | 0x00000008 | Used by UEFI Drivers that produce the Simple File System Protocol.               |
| `DEBUG_BLKIO`    | 0x00001000 | Used by UEFI Drivers that produce the Block I/O Protocols.                       |
| `DEBUG_NET`      | 0x00004000 | Used by UEFI Drivers that produce the network protocols other than NII and UNDI. |
| `DEBUG_UNDI`     | 0x00010000 | Used by UEFI Drivers that produce the NII Protocol and UNI interface.            |
| `DEBUG_LOADFILE` | 0x00020000 | Used by UEFI Drivers that produce the Load File Protocol.                        |
| `DEBUG_EVENT`    | 0x00080000 | Event messages. Used from event notification functions of UEFI Drivers.          |
| `DEBUG_LOAD`     | 0x00000004 | Load events. `DO NOT USE`.                                                       |
| `DEBUG_POOL`     | 0x00000010 | Pool allocations & frees. `DO NOT USE`.                                          |
| `DEBUG_PAGE`     | 0x00000020 | Page allocations & frees. `DO NOT USE`.                                          |
| `DEBUG_DISPATCH` | 0x00000080 | PEI/DXE/SMM Dispatchers. `DO NOT USE`.                                           |
| `DEBUG_VARIABLE` | 0x00000100 | Variable. `DO NOT USE`.                                                          |
| `DEBUG_BM`       | 0x00000400 | Boot Manager. `DO NOT USE`.                                                      |
| `DEBUG_GCD`      | 0x00100000 | Global Coherency Database changes. `DO NOT USE`.                                 |
| `DEBUG_CACHE`    | 0x00200000 | Memory range cache state changes. `DO NOT USE`.                                  |
