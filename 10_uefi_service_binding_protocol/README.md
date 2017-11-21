<!--- @file
  10 UEFI Service Binding Protocol

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

# 10 UEFI Service Binding Protocol

The Service Binding Protocol is not associated with a single GUID value.
Instead, each Service Binding Protocol GUID value is paired with another
protocol providing a specific set of services. The protocol interfaces for all
Service Binding Protocols are identical and contain the services
`CreateChild()` and `DestroyChild()`. When `CreateChild()` is called, a new
handle is created with the associated protocol installed. When `DestroyChild()` is called, the associated protocol is uninstalled and the handle is freed.

The _UEFI Specification_ defines the following Service Binding Protocol GUIDs.

###### Table 22-Service Binding Protocols

| **Service Binding Protocol**                   | **Associated Protocol**        |
| ---------------------------------------------- | ------------------------------ |
| `EFI_MANAGED_NETWORK_SERVICE_BINDING_PROTOCOL` | `EFI_MANAGED_NETWORK_PROTOCOL` |
| `EFI_ARP_SERVICE_BINDING_PROTOCOL`             | `EFI_ARP_PROTOCOL`             |
| `EFI_EAP_SERVICE_BINDING_PROTOCOL`             | `EFI_EAP_PROTOCOL`             |
| `EFI_IP4_SERVICE_BINDING_PROTOCOL`             | `EFI_IP4_PROTOCOL`             |
| `EFI_IP6_SERVICE_BINDING_PROTOCOL`             | `EFI_IP6_PROTOCOL`             |
| `EFI_TCP4_SERVICE_BINDING_PROTOCOL`            | `EFI_TCP4_PROTOCOL`            |
| `EFI_TCP6_SERVICE_BINDING_PROTOCOL`            | `EFI_TCP6_PROTOCOL`            |
| `EFI_UDP4_SERVICE_BINDING_PROTOCOL`            | `EFI_UDP4_PROTOCOL`            |
| `EFI_UDP6_SERVICE_BINDING_PROTOCOL`            | `EFI_UDP6_PROTOCOL`            |
| `EFI_MTFTP4_SERVICE_BINDING_PROTOCOL`          | `EFI_MTFTP4_PROTOCOL`          |
| `EFI_MTFTP6_SERVICE_BINDING_PROTOCOL`          | `EFI_MTFTP6_PROTOCOL`          |
| `EFI_DHCP4_SERVICE_BINDING_PROTOCOL`           | `EFI_DHCP4_PROTOCOL`           |
| `EFI_DHCP6_SERVICE_BINDING_PROTOCOL`           | `EFI_DHCP6_PROTOCOL`           |
| `EFI_HASH_SERVICE_BINDING_PROTOCOL`            | `EFI_HASH_PROTOCOL`            |

The Service Binding Protocol feature is required only if the associated
protocol requires a Service Binding Protocol to produce its services and it
defines a GUID value for that Service Binding Protocol. The table above lists
the protocols defined in the _UEFI Specification_ requiring the Service Binding
Protocol feature. None of the other protocols defined by the _UEFI
Specification_ require a Service Binding Protocol.

For new protocols, a decision must be made to determine if the new protocol
requires a Service Binding Protocol. The Driver Binding Protocol is usually
sufficient for managing devices on common bus topologies and for the simple
layering of protocols on a single device. When more complex tree or graph
topologies are required and, with the expectation that services of the new
protocol be required by multiple consumers, a Service Binding Protocol should
be considered.
