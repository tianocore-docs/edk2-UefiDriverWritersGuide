<!--- @file
  9 Driver Binding Protocol

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

# 9 Driver Binding Protocol

The Driver Binding Protocol provides services to do the following:

* Connect a driver to a controller.

* Disconnect a driver from a controller.

UEFI drivers following the UEFI driver model are required to implement the
Driver Binding Protocol. This requirement includes the following drivers:

* Device drivers

* Bus drivers

* Hybrid drivers

Root bridge driver, service drivers, and initializing drivers do not produce
this protocol.

The Driver Binding Protocol is the most important protocol that a driver
produces. It is the one protocol used by the UEFI boot services
`ConnectController()` and `DisconnectController()`. These UEFI boot services are used by the UEFI boot
manager to connect the console and boot devices required to boot an operating
system. The implementation of the Driver Binding Protocol varies depending upon
the driver's category. _Chapter 6_ of this guide describes the various driver
categories.
