<!--- @file
  22 Text Console Driver Design Guidelines

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

# 22 Text Console Driver Design Guidelines

This chapter covers the general guidelines for implementing UEFI Drivers for
devices that provide console services. This include devices that allow the user
to input information though key press actions such as a keyboard or keypad,
devices that provide text based output, and byte-stream devices like a UART
that can be connected to a remote terminal to provide console services.

If a device is intended to be used as a console input device and that device
must be available for use as a console input device while UEFI firmware is
active, then a UEFI Driver must be implemented that produces both the Simple
Text Input Protocol and the Simple Text Input Ex Protocol. The Simple Text In
Protocols are produced for any device that can behave like a basic keyboard.
This could be an actual keyboard such as USB or PS/2, a serial terminal, a
remote network terminal such as Telnet, or a custom device that provide the
ability for a user to perform actions that can be translated into UEFI
compatible keystroke information.

If a device is intended to be used as a console output device while UEFI
firmware is active, and that device is able to display text strings, then a
UEFI Driver must be implemented that produces the Simple Text Output Protocol.
The device must support an 80 column by 25 row character mode, and may
optionally support additional modes. The device must either directly support or
be able to emulate the following operations:
* Clear the display
* Scroll the display up
* Move cursor
* Turn cursor on and off
* Support 16 foreground colors
* Support 8 background colors

If a device is graphics controller that is able to emulate a text console using
bitmap fonts, then see _Chapter 23_ on the Graphics Output Protocol. The EDK II
provides a platform agnostic driver in the `MdeModulePkg` in the directory
`MdeModulePkg/Universal/Console/GraphicsConsoleDxe` that uses the services of a
Graphics Output Protocol and bitmap fonts to produce the Simple Text Output
Protocol.

If a device supports character based communication where data can be both
transmitted and received character at a time, and the goal is to use that
device for console services by connecting the device to terminal or terminal
emulator, then a UEFI Driver must be implemented that produces the Serial I/O
Protocol. This may include devices such as a UART style serial port or any
other character based I/O device on a motherboard, an add-in card, or USB.

The EDK II provides a terminal driver that supports the PC-ANSI, VT-100,
VT-100+, and VT-UTF8 terminal types. This terminal driver is in the
`MdeModulePkg` in the directory `MdeModulePkg/Universal/Console/TerminalDxe`.
This driver consumes the Serial I/O Protocol and produces all the
Simple Input Protocol, the Simple Input Ex Protocol, and the Simple Text Output Protocol.
