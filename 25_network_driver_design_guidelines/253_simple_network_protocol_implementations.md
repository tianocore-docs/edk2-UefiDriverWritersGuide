<!--- @file
  25.3 Simple Network Protocol Implementations

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

## 25.3 Simple Network Protocol Implementations

Exposing SNP instead of NII and UNDI has some advantages and some disadvantages
over using NII and UNDI. SNP-based network drivers are never UEFI Runtime
Drivers, so such drivers do not have to worry about meeting the UEFI Runtime
Driver requirements. This allows an SNP driver to be compiled for all the CPU
architectures supported by the _UEFI Specification_ including EBC. SNP may be
required for some nonstandard network interface controllers that cannot meet
the UNDI requirements.

When a network driver exposes SNP directly the system firmware layers MNP on
top of SNP and does not use its internal SNP driver as part of this network
stack.

The following figure shows a possible network stack based on a network driver
producing SNP. The inclusion of Load File Protocol is not guaranteed here, but
is a choice made by the system firmware.

###### Figure 30-SNP Based Network Stack

![](../media/image58.jpg)

The implementation of the Simple Network Protocol is typically found in the file
SimpleNetwork.c. Appendix A contains a template for a SimpleNetwork.c file for a UEFI Driver. The list of tasks to implement the Simple Network Protocol is as follows:

* Add global variable for the `EFI_SIMPLE_NETWORK_PROTOCOL` instance to
  `SimpleNetworkProtocol.c`.

* Create child handle in Driver Binding Protocol `Start()` and install the
  Simple Network Protocol and the Device Path Protocol. Also allocate and
  initialize an `EFI_SIMPLE_NETWORK_MODE` structure in the Simple Network
  Protocol.

* Implement the Simple Network Protocol services in `SimpleNetwork.c`.

* Create an Exit Boot Services Event to disable DMA when packets are received.

The following example shows the protocol interface structure for the Simple
Network Protocol for reference. This protocol is composed of 13 services, an
`EFI_EVENT` that can be used to poll when a packet has been received, and a
`Mode` structure that contains details on the attributes and capabilities of
the network interface controller.

###### Example 240-Simple Network Protocol

```c
typedef struct _EFI_SIMPLE_NETWORK_PROTOCOL EFI_SIMPLE_NETWORK_PROTOCOL;

///
/// The EFI_SIMPLE_NETWORK_PROTOCOL protocol is used to initialize access
/// to a network adapter. Once the network adapter initializes,
/// the EFI_SIMPLE_NETWORK_PROTOCOL protocol provides services that
/// allow packets to be transmitted and received.
///
struct _EFI_SIMPLE_NETWORK_PROTOCOL { ///
  /// Revision of the EFI_SIMPLE_NETWORK_PROTOCOL. All future revisions must
  /// be backwards compatible. If a future version is not backwards compatible
  /// it is not the same GUID.
  ///
  UINT64 Revision;
  EFI_SIMPLE_NETWORK_START Start;
  EFI_SIMPLE_NETWORK_STOP Stop;
  EFI_SIMPLE_NETWORK_INITIALIZE Initialize;
  EFI_SIMPLE_NETWORK_RESET Reset;
  EFI_SIMPLE_NETWORK_SHUTDOWN Shutdown;
  EFI_SIMPLE_NETWORK_RECEIVE_FILTERS ReceiveFilters;
  EFI_SIMPLE_NETWORK_STATION_ADDRESS StationAddress;
  EFI_SIMPLE_NETWORK_STATISTICS Statistics;
  EFI_SIMPLE_NETWORK_MCAST_IP_TO_MAC MCastIpToMac;
  EFI_SIMPLE_NETWORK_NVDATA NvData;
  EFI_SIMPLE_NETWORK_GET_STATUS GetStatus;
  EFI_SIMPLE_NETWORK_TRANSMIT Transmit;
  EFI_SIMPLE_NETWORK_RECEIVE Receive;
  ///
  /// Event used with WaitForEvent() to wait for a packet to be received.
  ///
  EFI_EVENT WaitForPacket;
  ///
  /// Pointer to the EFI_SIMPLE_NETWORK_MODE data for the device.
  ///
  EFI_SIMPLE_NETWORK_MODE *Mode;
};

extern EFI_GUID gEfiSimpleNetworkProtocolGuid;
```
