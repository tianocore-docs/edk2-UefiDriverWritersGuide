<!--- @file
  23.5 EDID Override Protocol Implementation

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

## 23.5 EDID Override Protocol Implementation

The UEFI platform firmware may produce `EFI_EDID_OVERRIDE_PROTOCOL` in a
platform specific driver implementation. This implementation of this protocol
is not the responsibility of the UEFI Driver that produces the Graphics Output
Protocol. If the UEFI platform firmware produces this protocol, then UEFI
Driver for a graphics controller must use this information when producing the
EDID Active Protocol on the same handle as the Graphics Output Protocol.

The implementation of the EDID Override Protocol is typically found in the
file EdidOverride.c. Appendix A contains a template for an EdidOverride.c file
for a platform specific UEFI Driver. The list of tasks to implement the EDID
Override Protocol is as follows:

* Add global variable for the `EFI_EDID_OVERRIDE_PROTOCOL` instance to
  `EdidOverride.c`.

* Implement the `GetEdid()` service in `EdidOverride.c`.

* The implementation of the EDID Override Protocol is typically in a Service
  Driver. This means that the EDID Override Protocol is typically installed
  onto a new handle in the Handle Database in the platform specific driver's
  entry point.

The following example shows the protocol interface structure for the EDID
Override Protocol for reference.

###### Example 235-DID Override Protocol

```c
typedef struct _EFI_EDID_OVERRIDE_PROTOCOL EFI_EDID_OVERRIDE_PROTOCOL;

///
/// This protocol is produced by the platform to allow the platform to provide
/// EDID information to the producer of the Graphics Output protocol.
///
struct _EFI_EDID_OVERRIDE_PROTOCOL {
  EFI_EDID_OVERRIDE_PROTOCOL_GET_EDID GetEdid;
};

extern EFI_GUID gEfiEdidOverrideProtocolGuid;
```
