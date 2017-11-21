<!--- @file
  14 Driver Health Protocol

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

# 14 Driver Health Protocol

The Driver Health Protocol is a feature potentially required by UEFI Drivers
following the UEFI Driver Model. If a UEFI Driver needs to report health status
to the platform, provide warning or error messages to the user, perform length
repair operations, or request that the user make hardware or software
configuration changes, the Driver Health Protocol must be produced. This
protocol is required only for devices potentially in a bad state and
recoverable through either a repair operation or configuration change. The
Driver Health Protocol should not be implemented if a device can never be in a
bad state or a device can be in a bad state for which no remediation is
possible.

The UEFI Boot Manager uses the services of the Driver Health Protocol, if
present, to determine the health status of a device and display that status
information on a UEFI console. The UEFI Boot Manager may also choose to perform
actions to transition devices from a bad state to a usable state. See the EFI
Driver Health Protocol section of the _UEFI Specification_ for more details on
how a UEFI Boot manager interacts with the Driver Health Protocol.

This chapter focuses on how to implement the Driver Health Protocol for a UEFI
Driver managing a specific set of devices.
