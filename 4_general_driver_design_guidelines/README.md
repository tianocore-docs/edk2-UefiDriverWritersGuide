<!--- @file
  4 General Driver Design Guidelines

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

# 4 General Driver Design Guidelines

This chapter contains general guidelines for the implementation of all types of
UEFI drivers. Guidelines for specific driver types (PCI, USB, SCSI, ATA,
Console, Graphics, Mass Storage, Network, etc.) are presented in individual
chapters later in this guide. This chapter also focuses on general guidelines
for implementing UEFI Drivers sources portable to all UEFI conformant platforms
and all CPU architectures supported by the _UEFI Specification_. If these guidelines are followed, there is a good chance that UEFI Drivers can be re-compiled for a different CPU architecture with no source code changes.

There are a few portability issues that apply specifically to IPF and EBC, and
these are presented in individual sections later in this guide as well. The
summary of topics covered includes:

* Common practices for C source code

* Maximizing Platform Compatibility

* Maximizing CPU Compatibility

* Optimizing for size and performance
