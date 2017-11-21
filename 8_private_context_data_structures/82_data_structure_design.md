<!--- @file
  8.2 Data structure design

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

## 8.2 Data structure design

Proper data structure design is one of the keys to making UEFI Drivers both
simple and easy to maintain. If a UEFI Driver writer fails to include fields in
a private context data structure, then it may require a complex algorithm to
retrieve the required data through the various UEFI services. By designing-in
the proper fields, these complex algorithms are avoided, resulting in a driver
with a smaller executable footprint. Static data, commonly accessed data, and services related to the management of
a device should all be placed in a private context data structure.

Another key requirement is that the private context data structure must be easy
to find when an I/O service produced by the driver is called. The I/O services
produced by a driver are exported through protocol interfaces, and all protocol
interfaces include a _This_ parameter as the first argument. The _This_
parameter is a pointer to the protocol interface containing the I/O service
being called. The data structure design presented here shows how the _This_
pointer passed into an I/O service can be used to easily gain access to the
private context data structure.

A private context data structure is typically composed of the following types
of fields:
* A signature for the data structure
* The handle of the controller or the child that is being managed or produced
* The group of protocol interfaces that are being consumed
* The group of protocol interfaces that are being produced
* Private data fields and services that are used to manage a specific controller

The signature is useful when debugging UEFI drivers. Signatures are composed of
four ASCII characters in a data field of type `UINTN` and must be the first
field of the structure with the field name of _Signature_. When memory dumps
are performed, signatures stand out by making the beginning of specific data
structures easy to identify. Memory dump tools with search capabilities can
also be used to find specific private context data structures in memory. In
addition, debug builds of UEFI drivers can perform signature checks whenever
these private context data structures are accessed. If the signature does not
match, then an `ASSERT()` may be generated. If one of these `ASSERT()` messages
is observed, a UEFI driver was likely passed in a bad or corrupt _This_ pointer
or the contents of the data structure that _This_ refers too has been corrupted.

Device drivers typically store the handle of the device they are managing in a
private context data structure. This mechanism provides quick access to the
device handle if needed during I/O operations or driver-related operations.
Root bridge drivers and bus drivers typically store the handle of the child
that was created, and a hybrid driver typically stores both the handle of the
bus controller and the handle of the child controller produced.

The group of consumed protocol interfaces is the set of pointers to the
protocol interfaces that are opened in the `Start()` function of the driver's
`EFI_DRIVER_BINDING_PROTOCOL`. As each protocol interface is opened using the
UEFI Boot Service `OpenProtocol()`, a pointer to the consumed protocol
interface is stored in the private context data structure. These same protocols
must be closed in the `Stop()` function of the driver's
`EFI_DRIVER_BINDING_PROTOCOL` with calls to the UEFI Boot Service
`CloseProtocol()`. Basically, the stop section should mirror the start section
of the driver, closing all protocols that were started.

The group of produced protocol interfaces declares the storage for the
protocols that the driver produces. These protocols typically provide software
abstractions for consoles or boot devices.

The number and type of private data fields vary from driver to driver. These
fields contain the context information for a device that is not contained in
the consumed or produced protocols. For example, a driver for a mass storage
device may store information about the characteristics of the mass storage
device such as the number of cylinders, number of heads, and number of sectors
on the physical mass storage device managed by the driver.

Appendix A contains the generic template for the `<<DriverName>>`.h file with
the declaration of a private context data structure that can be used for root
bridge drivers, device drivers, bus drivers, or hybrid drivers. The #define
statement above the private context data structure declaration using the
SIGNATURE_32() macro is used to initialize the Signature field when the private
context data structure is allocated. This same #define statement is used to
verify the Signature field whenever a driver accesses the private context data
structure.

A set of macros below the private context data structure declaration help
retrieve a pointer to the private context data structure from a _This_ pointer
for each of the produced protocols using the `CR()` macro introduced above.
These macros are the simple mechanisms that allow private data fields to be
accessed from the services in each of the produced protocols.

The example below shows an example of the private context data structure from
the `DiskIoDxe` driver in the `MdeModulePkg`. It contains the `#define`
statement for the data structure's signature. In this case, the signature is
the ASCII string "`dskI`". The example also contains a pointer to the only
protocol that this driver consumes; the Block I/O Protocol. It contains storage
for the only protocol this driver produces; the Disk I/O Protocol. It does not
have any additional private data fields. The macro at the bottom retrieves the
private context data structure from a pointer to the field called `DiskIo` that
is a pointer to the one protocol that this driver produces.

###### Example 113-Simple private context data structure

```c
#define DISK_IO_PRIVATE_DATA_SIGNATURE SIGNATURE_32 ('d','s','k','I')

typedef struct {
  UINTN Signature;
  EFI_DISK_IO_PROTOCOL DiskIo;
  EFI_BLOCK_IO_PROTOCOL *BlockIo;
} DISK_IO_PRIVATE_DATA;

#define DISK_IO_PRIVATE_DATA_FROM_THIS(a) \
  CR (a, DISK_IO_PRIVATE_DATA, DiskIo, DISK_IO_PRIVATE_DATA_SIGNATURE)
```

The following example shows a more complex private context data structure
from the `EhciDxe` driver in the `MdeModulePkg` that manages PCI EHCI
controllers and produces

USB Host Controller 2 Protocols. It contains the _Signature_ field that is set
to "`ehci`". It also contains pointers to the consumed protocol; the PCI I/O
Protocol, and storage for the USB Host Controller 2 Protocol that is produced
by this driver. In addition, there are a large number of private data fields
that are used during initialization and all supported USB transaction types.
Details on how these private fields are used can be found in the source code to
the EHCI driver in EDK II.

###### Example 114-Complex private context data structure

```c
#define USB2_HC_DEV_SIGNATURE SIGNATURE_32 ('e', 'h', 'c', 'i')

typedef struct {
  UINTN Signature;
  EFI_USB2_HC_PROTOCOL Usb2Hc;
  EFI_PCI_IO_PROTOCOL *PciIo;
  UINT64 OriginalPciAttributes;
  USBHC_MEM_POOL *MemPool;
  EHC_QTD *ShortReadStop;
  EFI_EVENT PollTimer;
  EFI_EVENT ExitBootServiceEvent;
  EHC_QH *ReclaimHead;
  VOID *PeriodFrame;
  VOID *PeriodFrameHost;
  VOID *PeriodFrameMap;
  EHC_QH *PeriodOne;
  LIST_ENTRY AsyncIntTransfers;
  UINT32 HcStructParams;
  UINT32 HcCapParams;
  UINT32 CapLen;
  EFI_UNICODE_STRING_TABLE *ControllerNameTable;
} USB2_HC_DEV;

#define EHC_FROM_THIS(a) \
  CR(a, USB2_HC_DEV, Usb2Hc, USB2_HC_DEV_SIGNATURE)
```
