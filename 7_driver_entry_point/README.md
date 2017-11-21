<!--- @file
  7 Driver Entry Point

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

# 7 Driver Entry Point

This chapter covers the entry point for the different categories of UEFI
drivers and their optional features impacting the implementation of the driver
entry point. The most common category of UEFI driver is one that follows the
UEFI driver model. This category of driver is discussed first, followed by the
other major types of drivers and the optional features those drivers may choose
to implement. The following categories of UEFI drivers are discussed:

* UEFI Driver that follows the UEFI Driver Model

* UEFI Runtime Driver

* Initializing driver

* Root bridge driver

* Service driver

The driver entry point is the function called when a UEFI driver is started
with the `StartImage()` service. At this point the driver has already been
loaded into memory with the `LoadImage()` service.

UEFI drivers use the PE/COFF image format that is defined in the _Microsoft
Portable Executable and Common Object File Format Specification_. This format
supports a single entry point in the code section of the image. The
`StartImage()` service transfers control to the UEFI driver at this entry point.

The _example below_ shows the entry point to a UEFI driver called `AbcDriverEntryPoint()`. This example is expanded upon as each of UEFI driver
categories and features are discussed. The entry point to a UEFI driver is identical to the standard UEFI image entry
point that is discussed in the UEFI Image Entry Point section of the _UEFI
Specification_. The image handle of the UEFI driver and a pointer to the UEFI
system table are passed into every UEFI driver. The image handle allows the
UEFI driver to discover information about itself, and the pointer to the UEFI
system table allows the UEFI driver to make UEFI Boot Service and UEFI Runtime
Service calls.

The UEFI driver can use the UEFI boot services to access the protocol
interfaces that are installed in the handle database, which allows the UEFI
driver to use the services that are provided through the various protocol
interfaces.

###### Example 86-UEFI Driver Entry Point 

```c
#include <Uefi.h>

/**
  This is the declaration of an EFI image entry point. This entry point
  Is the same for UEFI Applications, UEFI OS Loaders, and UEFI Drivers including both device drivers and bus drivers.
  @param  ImageHandle  The firmware allocated handle for the UEFI image. @param SystemTable A pointer to the EFI System Table.
  @retval  EFI_SUCCESS  The operation completed successfully. @retval Others An unexpected error occurred.
**/
EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  return EFI_SUCCESS;
}
```

The name of the driver entry point function must be declared in the `[Defines]` section of the INF file associated
with the UEFI Driver. The example below shows the INF file that defines
`ENTRY_POINT` to the `AbcDriverEntryPoint()` function shown in the previous
example. Where applicable, this INF file example is expanded upon as each of
UEFI driver categories and features are discussed. See _Section 30.3_ for more
details on UEFI Driver INF files and _Appendix A_ for a complete template of an
INF file for a UEFI Driver.

###### Example 87-UEFI Driver INF File

```ini
[Defines]
  INF_VERSION    = 0x00010005
  BASE_NAME      = AbcDriverMinimum
  FILE_GUID      = DA87D340-15C0-4824-9BF3-D52286674BEF
  MODULE_TYPE    = UEFI_DRIVER
  VERSION_STRING = 1.0
  ENTRY_POINT    = AbcDriverEntryPoint

[Sources]
  Abc.c

[Packages]
  MdePkg/MdePkg.dec

[LibraryClasses]
  UefiDriverEntryPoint
```

