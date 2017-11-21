<!--- @file
  29.8 UEFI Driver Entry Point

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

## 29.8 UEFI Driver Entry Point

The entry point to an EBC compiled image is a function is always called
`EfiStart()`. This is the function that is shown as the entry point in the
PE/COFF image that is produced by an EBC compile/link operation. The
`EfiStart()` function performs the EBC runtime initialization that may vary
from one UEFI Driver to another. At the end of the EBC runtime initialization,
the function `EfiMain()` is called. The EDK II build system and libraries take
care of these details, so a UEFI Driver implementation never contains functions
with these names. In fact, the symbols `EfiStart()` and `EfiMain()` must be
considered reserved, and cannot be used as function names or variable names in
any UEFI driver implementation that is compiled for EBC.

The INF file for a UEFI Driver declares the C entry point in the `[Defines]`
section in a define called `ENTRY_POINT`. All UEFI Drivers are linked to the
EDK II library instance from the `MdePkg` called `UefiDriverEntryPoint`, and
the `UefiDriverEntryPoint` library instance is responsible for calling the
library constructors for all the libraries that a UEFI Driver is using either
directly or indirectly. Once all the library constructors have been called,
control is transferred to the `ENTRY_POINT` function defined in the INF file.
This is where the C sources for a UEFI Driver implementation begin and the
driver specific initialization is performed.

The sequence of calls in a UEFI Driver entry point compiled for EBC is as
follows:

* `EfiStart()` - PE/COFF entry point that performs the required EBC runtime
  initialization. Calls `EfiMain()`.

* `EfiMain()` - Calls `_ModuleEntryPoint()`

* `_ModuleEntryPoint()` - Calls EDK II library constructors. Calls
  `ENTRY_POINT` function defined in INF file.

* `ENTRY_POINT` function - Performs UEFI Driver specific initialization.

Knowledge of this specific sequence of calls is not typically required by a
UEFI Driver developer because it is very rare for anything to go wrong in
`EfiStart()`, `EfiMain()` or `_ModuleEntryPoint()` functions. However, if a
UEFI Driver compiled for EBC is being debugged, it is important to know that
these extra actions do occur between the entry point of the PE/COFF image and
the first line of C source code in the UEFI Driver implementation.
