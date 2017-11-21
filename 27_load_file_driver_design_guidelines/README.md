<!--- @file
  27 Load File Driver Design Guidelines

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

# 27 Load File Driver Design Guidelines

The Load File Protocol is used to support booting from a device type which does
not fit cleanly into any of the standard device types supported by the _UEFI
Specification_. A UEFI Boot Manager can only boot through the Simple File
System Protocol or the Load File Protocol. If a device must be a boot device
and cannot directly or indirectly produce the Simple File System Protocol or
indirectly produce the Load File Protocol, then a Load File Protocol must be
implemented. The indirect production of Simple File System and the Load File
Protocol may not always be obvious. The EDK II provides a number of
platform-agnostic drivers that help produce the Simple File System Protocol and
the Load File Protocol through several layers of UEFI drivers. For example, a
UEFI Driver that produces the Block I/O Protocol is sufficient to produce the
Simple File System Protocol if the Disk I/O Driver, Partition Driver, and FAT
File System Driver are also included in the platform. Review all the other boot
device types described in this guide and the _UEFI Specification_ before
choosing to implement the Load File Protocol.

**********
**Note:** _The Load File Protocol should not be implemented for any standard
device type which has a defined driver hierarchy (e.g. USB, SCSI, and ATA)._
**********
