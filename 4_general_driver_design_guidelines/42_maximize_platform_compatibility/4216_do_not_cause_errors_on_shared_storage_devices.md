<!--- @file
  4.2.16 Do not cause errors on shared storage devices

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

### 4.2.16 Do not cause errors on shared storage devices

In a cluster configuration, multiple devices may be connected to a shared
storage. In such configurations, the UEFI driver should not cause errors that
can be seen by the other devices that are connected to storage.

**********
**Caution:** On a boot or reboot, there shall be no writes to shared storage
without user acknowledgement. Any writes to shared storage by a UEFI driver may
corrupt shared storage as viewed by another system. As a result, all
outstanding I/O in the controller's buffers will be cleared, as well as any
internal. Any I/O operations that occur after a reboot may corrupt shared
storage.
**********
**Caution:** There must not be an excessive number of bus or device resets.
Device resets have an impact on shared storage as viewed by other systems. For
a single reset, this impact is negligible. Larger numbers of resets may be seen
as a device failure by another system.
**********
**Caution:** Disk signatures must not be changed without warning the user. If
there is an impact to the user, then that impact should be displayed along with
the warning. Clusters may make an assumption about disk signatures on shared
storage.
**********
**Caution:** The discovery process must not impact other systems accessing the
storage. A long discovery process may "hold" drives and look like a failure of
shared storage.
**********
