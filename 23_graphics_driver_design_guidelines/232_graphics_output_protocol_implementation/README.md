<!--- @file
  23.2 Graphics Output Protocol Implementation

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

## 23.2 Graphics Output Protocol Implementation

The implementation of the Graphics Output Protocol is typically found in the file GraphicsOutput.c. Appendix A contains a template for a GraphicsOutput.c file for a UEFI Driver. The list of tasks to implement the Graphics Output Protocol is as follows: - Add global variable for the EFI_GRAPHICS_OUTPUT_PROTOCOL instance to GraphicsOutput.c. - Add global variable for the EFI_GRAPHICS_OUTPUT_PROTOCOL_MODE structure to GraphicsOutput.c.

* Implement the `QueryMode()`, `SetMode()`, and `Blt()` services in
  `GraphicsOutput.c`.

* Create a child handle for each output display controller and install the
  Graphics Output Protocol and a Device Path Protocol as described in the Rules
  for PCI/AGP Devices section of the _UEFI Specification_.

* If a graphics controller has the ability to read EDID information from
  display devices attached to an output controller, then install the EDID
  Discovered Protocol with the EDID data on the child handle associated with
  the output controller.

* Install the EDID Active Protocol with the EDID data on the child handle
  associated with the output controller. The EDID data comes from either the
  EDID Override Protocol provided by the platform or the EDID Discovered
  Protocol.

This example shows the protocol interface structure for the Graphics Output
Protocol for reference.

###### Example 231-Graphics Output Protocol

```c
typedef struct _EFI_GRAPHICS_OUTPUT_PROTOCOL EFI_GRAPHICS_OUTPUT_PROTOCOL;

///
/// Provides a basic abstraction to set video modes and copy pixels to and from
/// the graphics controller's frame buffer. The linear address of the hardware
/// frame buffer is also exposed so software can write directly to the video hardware.
///
struct _EFI_GRAPHICS_OUTPUT_PROTOCOL {
  EFI_GRAPHICS_OUTPUT_PROTOCOL_QUERY_MODE QueryMode;
  EFI_GRAPHICS_OUTPUT_PROTOCOL_SET_MODE SetMode;
  EFI_GRAPHICS_OUTPUT_PROTOCOL_BLT Blt;
  ///
  /// Pointer to EFI_GRAPHICS_OUTPUT_PROTOCOL_MODE data.
  ///
  EFI_GRAPHICS_OUTPUT_PROTOCOL_MODE *Mode;
};

extern EFI_GUID gEfiGraphicsOutputProtocolGuid;
```
