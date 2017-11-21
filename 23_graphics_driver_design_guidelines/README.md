<!--- @file
  23 Graphics Driver Design Guidelines

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

# 23 Graphics Driver Design Guidelines

This chapter covers the general guidelines for implementing UEFI Drivers for
graphics controllers. Most graphics controllers are PCI controllers, and this
implies that UEFI Drivers for graphics controllers are typically PCI drivers.
PCI drivers must follow all of the PCI design guidelines described in _Chapter
18_, as well as the general guidelines described in _Chapter 4_ of this guide.
Also see the Rules for PCI/AGP Devices section of the _UEFI Specification_.

If a device is intended to be used as a graphics console output device while
UEFI firmware is active, then a UEFI Driver must be implemented that produces
the Graphics Output Protocol. The graphics controller must either directly
support or be able to emulate the following operations:
* Block transfer to fill a region of the frame buffer
* Block transfer from system memory to region of frame buffer
* Block transfer from region of frame buffer to system memory
* Block transfer between two regions of the frame buffer
* Query attached display devices for EDID information
* Set the supported graphics modes that is intersection of modes that the
  graphics controller supports and the display device supports

The EDK II provides a platform agnostic driver in the `MdeModulePkg` in the
directory `MdeModulePkg/Universal/Console/GraphicsConsoleDxe` that uses the
services of a Graphics Output Protocol and bitmap fonts to produce the Simple Text Output
Protocol. This means if a Graphics Output Protocol is produced by a UEFI
Driver, then the frame buffer managed by that UEFI Driver can be used as a text
console device without having to implement the Simple Text Output Protocol in
the UEFI Driver for the graphics controller.
