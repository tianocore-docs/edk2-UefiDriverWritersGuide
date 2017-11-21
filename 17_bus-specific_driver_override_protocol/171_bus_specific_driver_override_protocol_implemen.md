<!--- @file
  17.1 Bus Specific Driver Override Protocol Implementation

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

## 17.1 Bus Specific Driver Override Protocol Implementation

The implementation of the Bus Specific Driver Override Protocol for a
specific bus driver is typically found in the file BusSpecificDriverOverride.c.
Appendix A contains a template for a BusSpecificDriverOverride.c file for a
UEFI Driver. The list of tasks to implement the Bus Specific Driver Override
Protocol feature are as follows:
* Add the `EFI_BUS_SPECIFIC_DRIVER_OVERRIDE_PROTOCOL` instance to the design of
  the private context data structure.
* Add private fields to the design of the private context data structure that
  support managing the set of driver image handles returned by `GetDriver()`.
* Update private content data structure initialization to initialize the Bus
  Specific Driver Override Protocol instance and the private fields used to
  manage the set of driver image handles returned by `GetDriver()`.
* Implement the `GetDriver()` service to return set-of-driver image handles
  from the private context data structure.
* Implement private worker functions to add/remove driver image handles from
  set-of-driver image handles maintained in the private context data structure.
* Install the Bus Specific Driver Override Protocol onto the child handle the
  bus driver produces in the Driver Binding `Start()` function.
* Uninstall the Bus Specific Driver Override Protocol from the child handle the
  bus driver produces in the Driver Binding `Stop()` function.

The Bus Specific Driver Override Protocol contains one service called
`GetDriver()` that returns an ordered list of driver image handles for the UEFI
drivers that were loaded from a container of UEFI driver(s). There are many
ways to implement storage for the ordered list of driver image handled
including an array and linked lists.

`PCI Use Case`: The order in which the image handles are returned by the PCI
Bus Driver matches the order in which the UEFI drivers were found in the PCI
option ROM, from the lowest address to the highest address. The PCI bus driver
is responsible for enumerating the PCI devices on a PCI bus. When a PCI device
is discovered, the PCI device is also checked to see if it has an attached PCI
option ROM. The PCI option ROM contents must follow the _PCI Specification_ for
storing one or more images. The PCI bus driver walks the list of images in a
PCI option ROM looking for UEFI drivers. If a UEFI driver is found, it is
optionally decompressed using the Decompress Protocol and then loaded The
driver entry point is called using the UEFI boot services `LoadImage()` and
`StartImage()`. If `LoadImage()` does not return an error, the UEFI driver must
be added to the end of the list of drivers the Bus Specific Driver Override Protocol
for that PCI device returns after the `GetDriver()` service is called.

The example below shows the protocol interface structure for the Bus Specific
Driver Override Protocol for reference and is composed of a single service
called `GetDriver()`.

###### Example 162-Bus Specific Driver Override Protocol

```c
typedef struct _EFI_BUS_SPECIFIC_DRIVER_OVERRIDE_PROTOCOL
  EFI_BUS_SPECIFIC_DRIVER_OVERRIDE_PROTOCOL;

///
/// This protocol matches one or more drivers to a controller. This protocol
/// is produced by a bus driver, and it is installed on the child handles of
/// buses that require a bus specific algorithm for matching drivers to
/// controllers.
///
struct _EFI_BUS_SPECIFIC_DRIVER_OVERRIDE_PROTOCOL {
  EFI_BUS_SPECIFIC_DRIVER_OVERRIDE_GET_DRIVER GetDriver;
};
```
