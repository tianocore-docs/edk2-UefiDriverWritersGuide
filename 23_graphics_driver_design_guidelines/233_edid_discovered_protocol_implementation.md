<!--- @file
  23.3 EDID Discovered Protocol Implementation

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

## 23.3 EDID Discovered Protocol Implementation

This protocol contains the EDID information that is retrieved from the display
device attached to a video output controller. This information may differ from
the EDID Active Protocol since the EDID Active Protocol takes into account any interaction with
the EDID Override Protocol that was consumed by this driver. This protocol is
installed on the child handle that represents a video output and must only
represent a single video output device. This protocol does not provide any
services. It only provides a pointer to a buffer with the EDID formatted data.

###### Example 233-EDID Discovered Protocol

```c
///
/// This protocol contains the EDID information retrieved from a video output
/// device.
///
typedef struct {
  ///
  /// The size, in bytes, of the Edid buffer. 0 if no EDID information
  /// is available from the video output device. Otherwise, it must be a
  /// minimum of 128 bytes.
  ///
  UINT32 SizeOfEdid;
  
  ///
  /// A pointer to a read-only array of bytes that contains the EDID
  /// information for an active video output device. This pointer is
  /// NULL if no EDID information is available for the video output
  /// device. The minimum size of a valid Edid buffer is 128 bytes.
  /// EDID information is defined in the E-DID EEPROM
  /// specification published by VESA (www.vesa.org).
  ///
  UINT8 *Edid;
} EFI_EDID_DISCOVERED_PROTOCOL;

extern EFI_GUID gEfiEdidDiscoveredProtocolGuid;
```
