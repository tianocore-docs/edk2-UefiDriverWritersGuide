<!--- @file
  20.1.7 Discover a SCSI channel

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

### 20.1.7 Discover a SCSI channel

It is recommended that the SCSI host controller driver construct a private
context structure for each enumerated SCSI channel. See _Chapter 8_ in this
guide for the advantage of using such a private context structure.

Specifically, the SCSI host controller driver should store all required
information for the child SCSI channel in this data structure, this should
including the signature, child handle value (optional for single channel
controller), channel number, and any produced protocols. This private context
structure can be accessed via the Record macro `CR()`, which is described in
_Chapter 8_ of this document.

The method for determining the number of channels on a given controller is chip
specific and varies by manufacturer. It is also the SCSI driver's
responsibility to do the following:
* Build the appropriate device path for the enumerated SCSI channel.
* Install Extended SCSI Pass Thru Protocol and Device Path Protocol on the
  appropriate handle (child handle is optional for single channel).
