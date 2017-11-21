<!--- @file
  4.2.18 Offer alternatives to function keys

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

### 4.2.18 Offer alternatives to function keys

Configuration of drivers should be accomplished via HII and via OS-present
interfaces.

There are design considerations when interacting outside of configuration.
First, consider using the setup interface as the user interface for a UEFI
driver. The user already understands the interface and remote use is already
enabled. If the existing high level interfaces cannot be used, then follow the
design considerations for using console based services.

UEFI drivers should use the console input services (see _Section 22.2_ of this
guide), and then be aware of alternatives to function keys. This is because the
UEFI console may be connected through a serial port. In such cases, it is
sensitive to the correct terminal emulator configuration. If the terminal
emulator is not correctly configured to match the terminal settings in UEFI (PC
ANSI, VT100, VT100+, or VT-UTF8), some of the keys (function keys, arrow keys
(page up/down, insert/delete, and backspace), may not work correctly, display
colors properly nor render the correct cursor positioning.

**********
**Note:** _To better support users, it is recommended that UEFI configuration
protocols and UEFI applications create user interfaces that are not solely
dependent on these keys but instead offer alternatives for these keys._
**********
**Note:** _It is important to be aware that the Simple Input Protocol does not
support the CTRL or ALT keys because these keys are not available with remote
terminals such as terminal emulators and telnet._
**********

The following table shows one possible set of alternate key sequences for
function keys, arrow keys, page up/down keys, and the insert/delete keys. Each
configuration protocol and application decides if alternate key sequences are
supported and which alternate mappings should be used. The table also lists the
UEFI scan code from the Simple Input Protocol and the alternate key sequence to
use to produce particular scan codes.

Most of these key sequences are directly supported in the EDK II_―_special
handling is not required to support these key sequences on a remote terminal.
Those labeled as "No" are not directly supported in the EDK II. They are parsed
and interpreted by the configuration protocol or application.

<div style="page-break-after: always;"></div>

###### Table 14-Alternate key sequences for remote terminals

| **UEFI scan code** | **Key sequence** | **Supported in EDK II?** |
| ---------------- | -------------- | ---------------------- |
| SCAN_UP          | '^'            | No                     |
| SCAN_DOWN        | 'v' or 'V'     | No                     |
| SCAN_RIGHT       | '>'            | No                     |
| SCAN_LEFT        | '<'            | No                     |
| SCAN_HOME        | ESC h          | Yes                    |
| SCAN_END         | ESC k          | Yes                    |
| SCAN_INSERT      | ESC +          | Yes                    |
| SCAN_DELETE      | ESC -          | Yes                    |
| SCAN_PAGE_UP     | ESC ?          | Yes                    |
| SCAN_PAGE_DOWN   | ESC /          | Yes                    |
| SCAN_F1          | ESC 1          | Yes                    |
| SCAN_F2          | ESC 2          | Yes                    |
| SCAN_F3          | ESC 3          | Yes                    |
| SCAN_F4          | ESC 4          | Yes                    |
| SCAN_F5          | ESC 5          | Yes                    |
| SCAN_F6          | ESC 6          | Yes                    |
| SCAN_F7          | ESC 7          | Yes                    |
| SCAN_F8          | ESC 8          | Yes                    |
| SCAN_F9          | ESC 9          | Yes                    |
| SCAN_F10         | ESC 0          | Yes                    |
| ESC              | ESC            | Yes                    |
