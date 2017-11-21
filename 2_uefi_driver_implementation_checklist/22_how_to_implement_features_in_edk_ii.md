<!--- @file
  2.2 How to implement features in EDK II

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

## 2.2 How to implement features in EDK II

The first column of the table below describes functions a typical driver
performs. Column 2 briefly describes how each function is implemented in UEFI
and references the chapter in this guide that specifically addresses each
issue. This list of driver operations is not exhaustive.

<div style="page-break-after: always;"></div>

###### Table 4-Mapping operations to UEFI drivers

| **Operation**                                                     | **Recommended UEFI method**                        |
| ----------------------------------------------------------------- | -------------------------------------------------- |
| Find devices that the driver supports while the driver is running | Do not try to search the handle database specifically. Instead, allow the supported section of the driver binding protocol to do this operation. <br/><br/> The supported section checks to see if the driver supports the device for the specified controller handle. The supported section uses the controller handle along with a partial device path, to check to see if the specific device is supported, and returns _supported, already started,_ or _notsupported_ for each device.  |
| Search devices that the driver supports                           | Use shell applications, such as the `dh` (dump handle database) command or the drivers shell command. <br/><br/> The `dh` command returns a list of all devices on the system. The drivers command returns a list of all drivers on the system. With the list of drivers, the `dh -d` command can be used to list the handles which that driver supports.   |
| Perform DMA                                                       | Use the DMA-related services from the PCI I/O Protocol. See the PCI driver section (_Chapter 18_) of this guide.    |
| Access PCI configuration header                                   | Always use PCI I/O Protocol services to access the PCI configuration header. Never directly access I/O ports 0xCF8 or 0xCFC. <br/><br/> See the PCI driver section (_Chapter 18_) of this guide.      |
| Access PCI I/O ports                                              | Always use PCI I/O Protocol services to access PCI I/O ports. Never use `IN` or `OUT` instructions. <br/><br/> See the PCI driver section (_Chapter 18_) of this guide.    |
| Access PCI memory                                                 | Use PCI I/O Protocol services to access PCI memory. Never use pointers to directly access memory-mapped I/O resources on a bus. <br/><br/> See the PCI driver section (_Chapter 18_) of this guide.    |
| Hardware interrupts                                               | EDK II does not support legacy INT type hooking interrupts. Instead, UEFI drivers are expected to either perform block I/O, by which they must complete their I/O operation and poll their devices as required to complete it, or they can create a periodic timer event to get control and check the status of the devices under management. <br/><br/> See the services section (_Chapter 5_) and the general driver guidelines section (_Chapter 4_) of this guide for more detail.     |
| Calibrated stalls                                                 | Do not use hardware devices to perform calibrated stalls. Instead, use the `Stall()` service for short delays that are typically less than 10 ms. Use one-shot timer events for long delays that are typically greater than 10 ms. Use `SetTimer()` in conjunction with `CreateEvent()`, or `CreateEventEx(`), for longer delays. Do not use the `GetTime()` service for delays in UEFI drivers. Use it only to retrieve information. See the services section in this guide: _Services that UEFI drivers commonly use_. |
| Get keyboard input from user                                      | Use the HII interface to accept keyboard input from the user. The HII engine displays forms to the user in which the user can answer questions or provide input. The forms themselves are defined in the VFR standard. <br/><br/> Note that console-related services, such as Simple Text Input Protocol and Simple Text Output Protocol can be replaced with or supplemented by HII functionality and forms. <br/><br/> Note that the Driver Configuration Protocol service is obsolete and has been replaced with HII functionality.|
| Display text                                                      | Use the HII interface to display text to the user. The HII engine displays forms to the user and allows querying of the user. The forms themselves are defined by the VFR programming language and IFR specification. <br/><br/> Note that console-related services, such as Simple Text Input Protocol and Simple Text Output Protocol, can be replaced with or supplemented by HII functionality and forms. <br/><br/> Also, note that the Driver Configuration Protocol service is obsolete and has been replaced with HII functionality. <br/><br/> Implement both the Driver Diagnostics Protocol and the Driver Diagnostics2 Protocol. See _Chapter 13_ of this guide. <br/><br/> UEFI drivers should not try to reprogram a flash device. Typically, a flash device is reprogrammed by a standalone application, such as a UEFI utility.    |
| Prepare controllers for use by an OS                              | The OS-present drivers should not make assumptions about the state of a controller. It should not assume a UEFI driver touched the controller before the OS was booted. If a specific state is required, then the driver can use an Exit Boot Services event to put the controller into the required state. See _Chapter 7_.   |
