<!--- @file
  12 UEFI Driver Configuration

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

# 12 UEFI Driver Configuration

The configuration of UEFI Drivers is typically provided through HII. If a UEFI
Driver requires interaction with a user to properly configure a device for use
in the UEFI preboot environment, HII packages must be registered and the HII
Config Access Protocol must be implemented. The requirement for HII packages
and the HII Config Access Protocol applies to UEFI Drivers required to be
compatible with platforms conformant with the _UEFI 2.1 Specification_ or
higher. This chapter focuses on guidelines for UEFI Drivers required to produce
HII based configuration methods.

If a UEFI Driver is required to be compatible with platforms conformant with
the _UEFI 2.0 Specification_, the Driver Configuration 2 Protocol must be implemented.
If a UEFI Driver is required to be compatible with platforms conformant with
the _UEFI 1.1 Specification_, the Driver Configuration Protocol must be
implemented.

UEFI platform firmware supporting HII provides an HII forms browser. This
component uses UEFI consoles to display configuration forms to the user and
allows the user to navigate between forms and within forms to answer questions
related to the configuration of devices.
