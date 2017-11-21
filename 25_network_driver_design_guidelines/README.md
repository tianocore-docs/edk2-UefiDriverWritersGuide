<!--- @file
  25 Network Driver Design Guidelines

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

# 25 Network Driver Design Guidelines

This chapter focuses on the design and implementation of UEFI Drivers for
network interface controllers. These UEFI Drivers typically bus drivers follow
the UEFI Driver Model. Some example devices include add-in PCI network
adapters, USB network controllers, cardbus network cards, and
LAN-on-motherboard network devices. This list illustrates that most network
interface controllers are PCI devices or USB devices. As a result, the UEFI
Drivers for network interface controllers must follow all of the design
guidelines described in _Chapter 18_ for PCI or _Chapter 19_ for USB, and must
also follow the general guidelines described in _Chapter 4_.

If a network interface controller is intended to be used as a boot device for a
UEFI operating system or UEFI applications, then a UEFI Driver must be
implemented that produces Network Interface Identifier Protocol and UNDI, the
Simple Network Protocol, or the Managed Network Protocol. If the network
interface controller hardware supports VLAN, then the VLAN Config Protocol must
be implemented. If the UEFI Driver for a network interface controller only
produces the Managed Network Protocol, then the UEFI Driver must also produce
the VLAN Config Protocol even if the network interface controller does not
support VLAN.

All three UEFI Driver designs for network interface controllers are covered in
this chapter. There are several factors that affect the design of a UEFI Driver
for a network interface controller. The following table summarizes the major
features for each of the three possible UEFI Driver designs.

###### Table 36-Network driver differences

| **Feature**                        | **NII and UNDI** | **Simple Network Protocol** | **Managed Network Protocol**  |
| -------------------------------------------- | ------ | ------------------ | ------------------ |
| UEFI Runtime Driver                          | Yes    | No                 | No                 |
| Depends on platform agnostic UEFI Driver for | Yes    | No                 | No                 |
| Depends on platform agnostic UEFI Driver for | Yes    | Yes                | No                 |
| Requires UNDI interface                      | Yes    | No                 | No                 |
| Supports EBC CPU Architecture                | No     | Yes                | Yes                |
| Requires Exit Boot Services Event            | Yes    | Maybe              | Maybe              |
| Requires Set Virtual Address Map Event       | Yes    | No                 | No                 |

The EDK II provides a set of platform agnostic drivers in the `MdeModulePkg`
and the `NetworkPkg` that consume the protocols produced by a UEFI Driver for a
network interface controller and produce the Load File Protocol which is one of
the two protocols from which a UEFI Boot Manager is able to boot a UEFI operating system or a
UEFI application. The Load File Protocol allows a UEFI operating system or UEFI
application to be booted from a properly configured PXE server. The platform
agnostic drivers allow the services provided by the network interface
controller to be accessed without any specialized knowledge of the specific
device or controller. The set platform agnostic UEFI Drivers include:

* `MdeModulePkg/Universal/Network/ArpDxe`

* `MdeModulePkg/Universal/Network/Dhcp4Dxe`

* `MdeModulePkg/Universal/Network/DpcDxe`

* `MdeModulePkg/Universal/Network/Ip4ConfigDxe`

* `MdeModulePkg/Universal/Network/Ip4Dxe`

* `MdeModulePkg/Universal/Network/IScsiDxe`

* `MdeModulePkg/Universal/Network/MnpDxe`

* `MdeModulePkg/Universal/Network/Mtftp4Dxe`

* `MdeModulePkg/Universal/Network/SnpDxe`

* `MdeModulePkg/Universal/Network/Tcp4Dxe`

* `MdeModulePkg/Universal/Network/Udp4Dxe`

* `MdeModulePkg/Universal/Network/UefiPxeBcDxe`

* `MdeModulePkg/Universal/Network/VlanConfigDxe`

* `NetworkPkg/Dhcp6Dxe`

* `NetworkPkg/Ip6Dxe`

* `NetworkPkg/IpSecDxe`

* `NetworkPkg/IScsiDxe`

* `NetworkPkg/Mtftp6Dxe`

* `NetworkPkg/TcpDxe`

* `NetworkPkg/Udp6Dxe`

* `NetworkPkg/UefiPxeBcDxe`

These platform agnostic drivers also provide support for iSCSI which produces
the Block I/O Protocol for a network boot target. Additional platform agnostic
drivers produce the Simple File System Protocol from a Block I/O Protocol.
Those details are provided in _Chapter 24_ on mass storage devices.
