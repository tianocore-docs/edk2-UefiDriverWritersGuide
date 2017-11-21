<!--- @file
  12.3 HII Protocols

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

## 12.3 HII Protocols

Some protocols must be clearly understood in order to successfully implement a
UEFI driver with HII functionality. The basic protocols consist of four
consumable protocols and the HII Config Access Protocol produced by a UEFI
Driver. They need not be used in any particular order. The `MdeModulePkg`
provides the `UefiHiiServicesLib` that automatically looks up consumed HII
protocols and makes them available to a UEFI Driver requiring the services they
provide. A UEFI platform is not required to produce all of these protocols. The
following is the list of protocols and the global variable provided by the
`UefiHiiServicesLib`. If a global variable is set to `NULL`, it means that the
platform does not produce that specific protocol. UEFI Drivers must handle all
platform configurations, so it is important for a UEFI Driver to continue to
function both when an HII related protocol is present and when an HII related
protocol is absent.

1. `EFI_HII_DATABASE_PROTOCOL gHiiDatabase`

2. `EFI_HII_STRING_PROTOCOL gHiiString`

3. `EFI_HII_FONT_PROTOCOL gHiiFont`

4. `EFI_HII_IMAGE_PROTOCOL gHiiImage`
