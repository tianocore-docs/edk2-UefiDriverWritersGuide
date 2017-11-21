<!--- @file
  25.2 NII Protocol and UNDI Implementations

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

## 25.2 NII Protocol and UNDI Implementations

Network drivers that follow the UNDI definition from the _UEFI Specification_
are unique compared to all others peripheral drivers.

* UEFI Drivers that produce UNDI interfaces must be UEFI Runtime Drivers. This
  allows a UEFI operation system to potentially use the services of this UEFI
  Runtime Driver to provide basic network connectivity in boot scenarios where
  the OS driver for the network interface controller is not available.

* UNDI is not a protocol interface. The Network Interface Identifier Protocol
  defines the entry point to the UNDI structure, but UNDI itself is not a
  protocol. The Command Descriptor Block (CDB) that the caller passed into each
  UNDI request must provide services that allow the UNDI to access the network
  interface controller hardware.

* See the Universal Network Driver Interfaces appendix of the _UEFI
  Specification_ for more details on UNDI adapters.

###### Figure 29-UEFI UNDI Network Stack

![](../../media/image56.jpg)

The implementation of the Network Interface Identifier Protocol is typically
found in the file NiiUndi.c. Appendix A contains a template for a NiiUndi.c
file for a UEFI Driver. The list of tasks to implement the Network Interface
Identifier Protocol and UNDI is as follows:

* Add global variable for the `EFI_NETWORK_INTERFACE_IDENTIFIER_PROTOCOL`
  instance to `NiiUndi.c`.

* Implement the UNDI interface in `NiiUndi.c`.

* Create child handle in Driver Binding Protocol `Start()` and install the NII
  Protocol and the Device Path Protocol.

* Create an Exit Boot Services Event to disable DMA when packets are received.

* Create a Set Virtual Address Map Event to convert physical addresses to
  virtual addresses.

The following example shows the protocol interface structure for the Network
Interface Identifier Protocol for reference. The Network Interface Identifier
Protocol is different from many other protocols in that it has no functions
inside it, and instead is only composed of data fields. These data fields share
information with the platform about the network interface controller
capabilities. The field called `Id` provides the address of a data structure
for the UNDI that includes methods for the platform to call the UNDI interfaces.

###### Example 239-Network Interface Identifier Protocol

```c
typedef struct _EFI_NETWORK_INTERFACE_IDENTIFIER_PROTOCOL
  EFI_NETWORK_INTERFACE_IDENTIFIER_PROTOCOL;
  
///
/// An optional protocol that is used to describe details about the software
/// layer that is used to produce the Simple Network Protocol.
///
struct _EFI_NETWORK_INTERFACE_IDENTIFIER_PROTOCOL {
  ///
  /// The revision of the EFI_NETWORK_INTERFACE_IDENTIFIER protocol.
  ///
  UINT64 Revision;
  ///
  /// The address of the first byte of the identifying structure for this network
  /// interface. This is only valid when the network interface is started
  /// (see Start()). When the network interface is not started, this field is set
  /// to zero.
  ///
  UINT64 Id;
  ///
  /// The address of the first byte of the identifying structure for this
  /// network interface. This is set to zero if there is no structure.
  ///
  UINT64 ImageAddr;
  ///
  /// The size of unrelocated network interface image.
  ///
  UINT32 ImageSize;
  ///
  /// A four-character ASCII string that is sent in the class identifier field of
  /// option 60 in DHCP. For a Type of EfiNetworkInterfaceUndi, this field is UNDI.
  ///
  CHAR8 StringId[4];
  ///
  /// Network interface type. This will be set to one of the values
  /// in EFI_NETWORK_INTERFACE_TYPE.
  ///
  UINT8 Type;
  ///
  /// Major version number.
  ///
  UINT8 MajorVer;
  ///
  /// Minor version number.
  ///
  UINT8 MinorVer;
  ///
  /// TRUE if the network interface supports IPv6; otherwise FALSE.
  ///
  BOOLEAN Ipv6Supported;
  ///
  /// The network interface number that is being identified by this Network
  /// Interface Identifier Protocol. This field must be less than or equal
  /// to the IFcnt field in the !PXE structure.
  ///
  UINT8 IfNum;
};

extern EFI_GUID gEfiNetworkInterfaceIdentifierProtocolGuid_31;
```

The following table shows the data structure called `!PXE` that resides at
address specified by the `Id` field of the Network Interface Identifier
Protocol.

###### Table 37-!PXE interface structure

|<td colspan="5" style="font-weight:bold">!PXE SW UNDI</td> |
| ------ | ---------- | ---------- | --------- | ---------- |
| Offset | 0x00       | 0x01       | 0x02      | 0x03       |
| 0x00    <td colspan=4>Signature</td>                      |
| 0x04   | Len        | Fudge      | Rev       | IFcnt      |
| 0x08   | Major      | Minor   <td colspan=2>Reserved</td> |
| 0x0C   <td colspan="4">Implementation</td>                  |
| 0x10   <td colspan=4>Entry Point</td>                     |
| 0x14   <td colspan=4>Entry Point</td>                     |
| 0x18   <td colspan=2>Reserved</td><td colspan=2>#bus</td> |
| 0x1C   <td colspan=4>Bus Types(s)                         |
| 0x20   <td colspan=4>More Bus Types(s)                    |

This table shows the layout of the Command Descriptor Block (CDB) structure
that is passed into the function specified by the Entry Point field of the !PXE
structure.

###### Table 38-CDB structure

|<td colspan="5" style="font-weight:bold; align:center">Command descriptor block (CDB)</td>|
| ------ | ----------- | ------------- |------------- | ------------ |
| Offset | 0x00        |  0x01         | 0x02         | 0x03         |
| 0x00   <td colspan=2>OpCode</td>    <td colspan=2>OpFlags</td>     |
| 0x04   <td colspan=2>CPBsize</td>     <td colspan=2>DBsize</td>    |
| 0x08   <td colspan=4>CPBaddr</td>                                  |
| 0x0C   <td colspan=4>CPBaddr</td>                                  |
| 0x10   <td colspan=4>DBaddr</td>                                   |
| 0c14   <td colspan=4>DBaddr</td>                                   |
| 0x18   <td colspan=2>StatCode</td> <td colspan=2>StatFlags</td>    |
| 0x1C   <td colspan=2>IFnum</td> <td colspan=2>Control</td>         |

The UEFI Driver for a network interface controller that implements an UNDI must
implement all the UNDI related OpCodes required by the _UEFI Specification_.
