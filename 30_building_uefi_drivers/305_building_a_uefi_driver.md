<!--- @file
  30.5 Building a UEFI driver

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

## 30.5 Building a UEFI driver

Building a UEFI Driver involves the use of the `build.exe` command provided
with the

EDK II tools. If the pre-requisites were followed at the beginning of this
chapter, then the only flag that need to be passed into `build.exe` is the DSC
file that is to be used for the build.

  `build -p MyDriverPkg/MyDriverPkg.dsc`

If the build competes successfully, then the UEFI Driver generated can be found
in the build output directory that is specified in the DSC file. In the example
above, `OUTPUT_DIRECTORY` is set to `Build/MyDriverPkg`. The following
example shows where `MyDriver.efi` is located. This specific example shows
that a `DEBUG` build was used with a Microsoft family compiler to generate
`MyDriver.efi` for IA32.

###### Example 264-Build Output Directory

```
Build\
MyDriverPkg\
DEBUG_VS2005x86\
IA32\
MyDriver.efi
```

If a UEFI Driver needs to be built as a `DEBUG` build or a `RELEASE` build,
this can be specified on the command line. The following two examples show how
to build for `DEBUG` and `RELEASE`. If the `-b` flag is not specified, then the
build type is retrieved from `Conf/target.txt`.
* `build -b DEBUG -p MyDriverPkg/MyDriverPkg.dsc`
* `build -b RELEASE -p MyDriverPkg/MyDriverPkg.dsc`

If a UEFI Driver needs to be built for other CPU architectures, then those can
also be specified on the command line. The following 4 examples show how to
build for IA32, X64, IPF, and EBC if the compiler and linkers installed support
all these architectures.
* `build -a IA32 -p MyDriverPkg/MyDriverPkg.dsc`
* `build -a X64 -p MyDriverPkg/MyDriverPkg.dsc`
* `build -a IPF -p MyDriverPkg/MyDriverPkg.dsc`
* `build -a EBC -p MyDriverPkg/MyDriverPkg.dsc`

The 4 separate commands above can also be combined into a single command:

* `build -a IA32 -a X64 -a IPF -a EBC -p MyDriverPkg/MyDriverPkg.dsc`

The EDK II also supports a configuration file for builds in the file path
`Conf/target.txt`.

This file may be updated with the specific configuration that is most commonly
used. For example, the `ACTIVE_PLATFORM` can be set to
`MyDriverPkg/MyDriverPkg.dsc`, and the build command can then be invoked with
no parameters at all:

* `build`

Please see the EDK II User's Manual and other EDK II documents for more details
on how to use the build command and for details on INF files, DEC files, and
DSC files.
