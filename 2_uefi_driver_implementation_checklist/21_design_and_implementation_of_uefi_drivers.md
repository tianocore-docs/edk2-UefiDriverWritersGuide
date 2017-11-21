<!--- @file
  2.1 Design and implementation of UEFI drivers

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

## 2.1 Design and implementation of UEFI drivers

The following lists the basic steps a driver writer should follow when
designing and implementing a UEFI driver. Note that this document assumes UEFI
driver model drivers are being developed.

1. Determine the category of UEFI driver to be developed. The different
   categories are listed in _Table 2_, below, and are described in more detail
   in _Chapter 6_ of this document.
   **********
   **Note:** _UEFI Drivers that follow the UEFI Driver Model are recommended because they enable faster platform boot times._
   **********

2. Make sure the driver supports the unload service. This feature is strongly
   recommended for all drivers. _Section 7.6_ describes the unload service.

3. Make sure the UEFI driver supports both the Component Name protocol and the
   Component Name2 protocol. It is strongly recommended that all drivers
   support both protocols.

4. Is the UEFI driver going to include configuration settings that the user can
   change? If so, the driver must support HII functionality. Note that the HII
   functionality replaces the Driver Configuration Protocol, which is now
   obsolete. See Table 2.

5. The UEFI driver must produce the Driver Diagnostics Protocols if the driver
   is going to support testing See _Chapter 13_.

6. If the UEFI driver is a bus driver for a bus type that supports storage of
   UEFI drivers with the child devices, the Bus Specific Driver Override
   Protocol must be implemented by the bus driver. See _Chapter 17_ of this
   guide.

7. A UEFI driver might not need to call an Exit Boot Service event. However, if
   the UEFI driver is going to require an Exit Boot Services event, then the
   driver must create an event of type Exit Boot Services. When the driver
   initializes, it creates the event, and when Exit Boot Services happens, the
   system calls the function that the driver produces. See _Chapter 7_.

8. For runtime drivers, make sure the driver defines an event of type Set
   Virtual Address Map. This allows the driver to know where the memory map is
   located once the OS takes control. See _Chapter 7_.

9. Identify the I/O-related protocols the driver needs to consume. Based on the
   list of consumed protocols and the criteria for these protocol interfaces,
   determine how many instances of the Driver Binding Protocol need to be
   produced. For example, a console driver might have multiple binding
   protocols to allow for input from multiple devices. See _Chapter 9_.

10. Identify all I/O-related protocols that the driver binding model must
   produce. Once the I/O-related protocols are known, make sure the driver
   creates a function with a single entry point for each protocol.

11. Implement the driver's entry point. See _Chapter 7_.

12. Design the private context data structure. See _Chapter 8_.

13. Implement all the services listed in the supported section of the Driver
   Binding Protocol. See _Chapter 9_. See Table 3.

###### Table 2-Classes of UEFI drivers to develop
| **Class of Driver**                                                          | **See sections**  |
| ---------------------------------------------------------------------------- | ----------------- |
| Device driver                                                                | _6.1_             |
| Bus driver that can produce one or all child handles                         | _6.2.6_           |
| Bus driver that produces all child handles in the first call to `Start()`    | _6.2.7_           |
| Bus driver that produces at most one child handle in `Start()`               | _6.2.8_           |
| Bus driver that produces no child handles in `Start()`                       | _6.2.9_           |
| Bus driver that produces child handles with multiple parent controllers      | _6.2.4_           |
| Hybrid driver that can produce one or all child handles                      | _6.3_ and _6.2.6_ |
| Hybrid driver that produces all child handles in the first call to `Start()` | _6.3_ and _6.2.7_ |
| Hybrid driver that produces at most one child handle in `Start()`            | _6.3_ and _6.2.8_ |
| Hybrid driver that produces no child handles in `Start()`                    | _6.3_ and _6.2.9_ |
| Hybrid driver that produces child handles with multiple parent controllers   | _6.3_ and _6.2.4_ |
| Service driver                                                               | _6.4_ and _7.9_   |
| Root bridge driver                                                           | _6.5_ and _7.10_  |
| Initializing driver                                                          | _6.6_ and _7.8_   |

<div style="page-break-after: always;"></div>

###### Table 3-Protocols produced by various devices
| **Device**                         | **Produces these I/O protocols**                              |
| ---------------------------------- | ------------------------------------------------------------- |
| USB peripherals                    | USB I/O protocol                                              |
| PCI adapter                        | PCI I/O protocol                                              |
| Console devices                    | Simple input protocol <br/><br/> Simple pointer protocol <br/><br/> Graphics output protocol <br/><br/> Block I/O protocol                                            |
| SCSI, SCSI RAID, and Fiber Channel | Extended SCSI pass thru protocol <br/><br/> Block I/O protocol                                            |
| NIC (network interface controller) | The protocols produced by the NIC depends on the specific NIC: <br/><br/> Universal network driver interface (UNDI) protocol <br/><br/> Network interface identifier protocol <br/><br/> Managed network protocol (MNP) <br/><br/> Simple network protocol (SNP)  |

**********
**Note:** _The device path protocol is a data structure protocol, not a
function call with a callable entry point. It is the UEFI driver's job to
append the path of the devices it is controlling to the data structure. In
other words, as part of producing the I/O protocol for each device, the driver
builds the device path for that device._
**********
