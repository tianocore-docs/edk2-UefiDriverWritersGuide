<!--- @file
  11.4 Testing Component Name Protocols

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

## 11.4 Testing Component Name Protocols

Use the UEFI Shell's `drivers` and `devices` commands to exercise the Component
Name Protocols. Running these commands with no options shows the sets of
drivers and devices in the platform The names are shown in the currently set
platform language.

These commands also support a -l option to request names in an alternate
language. _Figure 15_, following, shows an example using the UEFI Shell command `drivers`
on the EDK II Nt32 platform. _Figure 16_ then shows an example of using the
UEFI Shell command `devices` on the EDK II Nt32 platform. The details on each
UEFI Shell command available to test UEFI Drivers can be found in _Chapter 31_
of this guide.

###### Figure 15-Testing Component Name Protocol GetDriverName()

![](../media/image30.jpg)

<div style="page-break-after: always;"></div>

###### Figure 16-Testing Component Name Protocol GetControllerName()

![](../media/image32.jpg)

