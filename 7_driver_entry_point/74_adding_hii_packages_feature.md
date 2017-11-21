<!--- @file
  7.4 Adding HII Packages Feature

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

## 7.4 Adding HII Packages Feature

HII packages provide strings, fonts, and forms that allow users (such as IT
administrators) to change the configuration of UEFI managed devices. These HII
packages are required only if a driver must provide the ability for a user to
change configuration settings for a device. A UEFI Driver registers HII
packages in the HII Database.

The Image Services and the Human Interface Infrastructure Overview sections of
the _UEFI Specification_ define a method for HII packages associated with a
UEFI Driver to be automatically installed as a protocol on _ImageHandle_ when
the UEFI Driver is loaded using the UEFI Boot Service `LoadImage()`. The HII
packages are stored in a resource section of the PE/COFF image. The driver
entry point of a UEFI Driver is responsible for looking up the HII Package List
on _ImageHandle_ and registering that list of HII packages into the HII
Database. The example below shows an example of a driver entry point that
performs such a registration process.

###### Example 95-HII Packages feature

```c
#include <Uefi.h>
#include <Protocol/HiiDatabase.h>
#include <Protocol/HiiPackageList.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Library/DebugLib.h>

EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  EFI_STATUS                   Status;
  EFI_HII_PACKAGE_LIST_HEADER  *PackageListHeader;
  EFI_HII_DATABASE_PROTOCOL    *HiiDatabase;
  EFI_HII_HANDLE               HiiHandle;
  
  //
  // Retrieve HII Package List Header on ImageHandle
  //
  Status = gBS->OpenProtocol (
                  ImageHandle,
                  &gEfiHiiPackageListProtocolGuid,
                  (VOID **)&PackageListHeader,
                  ImageHandle,
                  NULL,
                  EFI_OPEN_PROTOCOL_GET_PROTOCOL
                  );
  ASSERT_EFI_ERROR (Status);
  
  //
  // Retrieve the pointer to the UEFI HII Database Protocol
  //
  Status = gBS->LocateProtocol (
                  &gEfiHiiDatabaseProtocolGuid,
                  NULL,
                  (VOID **)&HiiDatabase
                  );
  ASSERT_EFI_ERROR (Status);
  
  //
  // Register list of HII packages in the HII Database
  //
  Status = HiiDatabase->NewPackageList (
                          HiiDatabase,
                          PackageListHeader,
                          NULL,
                          &HiiHandle
                          );
  ASSERT_EFI_ERROR (Status);
  
  return EFI_SUCCESS;
}
```

The EDK II provides a simple way for a UEFI Driver to declare that HII packages
are provided by setting `UEFI_HII_RESOURCE_SECTION` to `TRUE` in the
`[Defines]` section of the INF file. This informs an EDK II build that the UEFI
Driver implementation provides UNI and VFR source files that must be converted
into HII packages stored in the PE/COFF resource section of the UEFI Driver
image. See _Chapter 12_ for more details on the implementation of UNI and VFR
files. The following example shows the INF file that defines
`UEFI_HII_RESOURCE_SECTION` to `TRUE`. See _Section 30.3_ for more details on
UEFI Driver INF files and _Appendix A_ for a complete template of the INF file for a
UEFI Driver.

###### Example 96-UEFI Driver INF File with HII Packages feature

```ini
[Defines]
  INF_VERSION               = 0x00010005
  BASE_NAME                 = AbcDriverHiiPackage
  FILE_GUID                 = 0E474237-D123-40c2-A585-CD46279879D4
  MODULE_TYPE               = UEFI_DRIVER
  VERSION_STRING            = 1.0
  ENTRY_POINT               = AbcDriverEntryPoint
  UEFI_HII_RESOURCE_SECTION = TRUE

[Sources]
  Abc.c
  AbcStrings.uni
  AbcForms.vfr

[Packages]
  MdePkg/MdePkg.dec

[LibraryClasses]
  UefiDriverEntryPoint

[Protocols]
  gEfiHiiPackageListProtocolGuid gEfiHiiDatabaseProtocolGuid
```
