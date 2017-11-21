<!--- @file
  A.3.2 _ExtScsiPassThru.c File_

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

### A.3.2 _ExtScsiPassThru.c File_

###### Example A-14-Extended SCSI Pass Thru Protocol implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include "<<DriverName>>.h"

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_EXT_SCSI_PASS_THRU_PROTOCOL g<<DriverName>>ExtScsiPassThru = { NULL,
  <<DriverName>>ExtScsiPassThruPassThru,
  <<DriverName>>ExtScsiPassThruGetNextTargetLun,
  <<DriverName>>ExtScsiPassThruBuildDevicePath,
  <<DriverName>>ExtScsiPassThruGetTargetLun,
  <<DriverName>>ExtScsiPassThruResetChannel,
  <<DriverName>>ExtScsiPassThruResetTargetLun,
  <<DriverName>>ExtScsiPassThruGetNextTarget
};

EFI_STATUS
EFIAPI
<<DriverName>>ExtScsiPassThruPassThru (
  IN     EFI_EXT_SCSI_PASS_THRU_PROTOCOL             *This,
  IN     UINT8                                       *Target,
  IN     UINT64                                      Lun,
  IN OUT EFI_EXT_SCSI_PASS_THRU_SCSI_REQUEST_PACKET  *Packet,
  IN     EFI_EVENT                                   Event     OPTIONAL
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>ExtScsiPassThruGetNextTargetLun (
  IN     EFI_EXT_SCSI_PASS_THRU_PROTOCOL             *This,
  IN OUT UINT8                                       **Target,
  IN OUT UINT64                                      *Lun
  )
{ 
}

EFI_STATUS
EFIAPI
<<DriverName>>ExtScsiPassThruBuildDevicePath (
  IN     EFI_EXT_SCSI_PASS_THRU_PROTOCOL             *This,
  IN     UINT8                                       *Target,
  IN     UINT64                                      Lun,
  IN OUT EFI_DEVICE_PATH_PROTOCOL                    **DevicePath
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>ExtScsiPassThruGetTargetLun (
  IN  EFI_EXT_SCSI_PASS_THRU_PROTOCOL                *This,
  IN  EFI_DEVICE_PATH_PROTOCOL                       *DevicePath,
  OUT UINT8                                          **Target,
  OUT UINT64                                         *Lun
  )
{
}
EFI_STATUS
EFIAPI
<<DriverName>>ExtScsiPassThruResetChannel (
  IN EFI_EXT_SCSI_PASS_THRU_PROTOCOL                 *This
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>ExtScsiPassThruResetTargetLun (
  IN EFI_EXT_SCSI_PASS_THRU_PROTOCOL                 *This,
  IN UINT8                                           *Target,
  IN UINT64                                          Lun
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>ExtScsiPassThruGetNextTarget (
  IN     EFI_EXT_SCSI_PASS_THRU_PROTOCOL             *This,
  IN OUT UINT8                                       **Target
  )
{
}
```
