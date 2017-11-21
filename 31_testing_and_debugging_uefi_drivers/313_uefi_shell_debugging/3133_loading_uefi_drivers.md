<!--- @file
  31.3.3 Loading UEFI drivers

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

### 31.3.3 Loading UEFI drivers

The following table lists the UEFI Shell commands that are available to load
and start UEFI drivers.

<div style="page-break-after: always;"></div>

###### Table 42-UEFI Shell commands for loading UEFI drivers

| **Command** | **Description**                                                |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Load       | Loads a UEFI driver from a file. UEFI driver files typically have an extension of `.efi`. The `Load` command has one important option, the `-nc` ("No Connect") option, for UEFI driver developers. When the `Load` command is used without the `-nc` option, then the loaded driver is automatically connected to any devices in the system that it is able to manage. This means that the UEFI driver's entry point is executed and then the UEFI Boot Service `ConnectController()` is called. If the UEFI driver produces the Driver Binding Protocol in the driver's entry point, then the `ConnectController()` call exercises the `Supported()` and `Start()` services of Driver Binding Protocol that was produced. If the `-nc` option is used with the `Load` command, then this automatic connect operation is not performed. Instead, only the UEFI driver's entry point is executed. When the `-nc` option is used, the UEFI Shell command `connect` can be used to connect the UEFI driver to any devices in the system that it is able to manage. The `Load` command can also take wild cards, so multiple UEFI drivers can be loaded at the same time. The code below shows the following examples of the `Load` command: <br/><br/> * Example 1: Loads and does not connect the UEFI driver image `EfiDriver.efi`. This example exercises only the UEFI driver's entry point:<br/> `fs0:> Load -nc EfiDriver.efi` <br/><br/> * Example 2: Loads and connects the UEFI driver image called `EfiDriver.efi`. This example exercises the UEFI driver's entry point and the `Supported()` and `Start()` functions of the Driver Binding Protocol:<br/> `fs0:> Load EfiDriver.efi`  <br/><br/> * Example 3: Loads and connects all the UEFI drivers with an `.efi` extension from `fs0:`, exercising the UEFI driver entry points and their `Supported()` and `Start()` functions of the Driver Binding Protocol:<br/> `fs0:> Load *.efi`  |
| LoadPciRom | This command used to simulate the load of a PCI option ROM by the PCI bus driver. It also supports the `-nc` flag like the `Load` command, but takes the name of a PCI Option ROM file instead of an `.efi` file. |
