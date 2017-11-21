<!--- @file
  19.1.4 USB 2 Host Controller Protocol Data Transfer Services

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

### 19.1.4 USB 2 Host Controller Protocol Data Transfer Services

The USB2 Host Controller Protocol provides an I/O abstraction for a USB host
controller. A USB host controller is a hardware component that interfaces to a
Universal Serial Bus (USB). It moves data between system memory and devices on
the Universal Serial Bus by processing data structures and generating
transactions on the Universal Serial Bus.

This protocol is used by a USB bus driver to perform all data transactions over
the Universal Serial Bus. It also provides services to manage the USB root hub
integrated into the USB host controller.

_Appendix A_ provides a template for the implementation of the USB Host
Controller Protocol. The services of the USB 2 Host Controller Protocol can be
categorized into the following categories:
* Host controller general information
* GetCapability()
* Root hub-related services:
* GetRootHubPortStatus()
* SetRootHubPortFeature()
* ClearRootHubPortFeature()
* Host controller state-related services:
* GetState()
* SetState()
* Reset()
* USB transfer-related services:
* ControlTransfer()
* BulkTransfer()
* AsyncInterruptTransfer()
* SyncInterruptTransfer()
* IsochronousTransfer()
* AsyncIsochronousTransfer()

For root hub-related services and host controller state-related services,
implementation mainly involves read/write operations to specific USB host
controller registers. The USB host controller data sheet provides information
on these register usages, so this topic is not covered in detail here.

This section concentrates on the USB transfer-related services. Those transfers
are categorized as either _asynchronous_ or _synchronous._

With asynchronous transfers, the transfer does not complete with the service's
return. With synchronous transfers, the requested transfer has completed when
the service returns. The following sections discuss these two types of
transfers in more detail.

#### 19.1.4.1 Synchronous transfer

The USB Host Controller Protocol provides the following four synchronous
transfer services:
* `ControlTransfer()`
* `BulkTransfer()`
* `SyncInterruptTransfer()`
* `IsochronousTransfer()`

Control and bulk transfers are completed in an acceptable period of time and
thus are natural synchronous transfers in the view of an UEFI system.

Interrupt transfers and isochronous transfers can be either asynchronous or
synchronous transfers, depending on the usage model.

It is convenient for the USB drivers to use synchronous transfer services
because there is no worry about when the data is ready. The transfer result is
available as soon as the function returns.

The following is an example of how to use `BulkTransfer()` to implement a
synchronous transfer service. Generally speaking, implementing a bulk transfer
service can be divided into the following steps:
* `Preparation:` For example, USBSTS is a status register in the USB host
  controller. The status register needs to be cleared before starting the
  control transfer.
* `Setting up the DMA direction:` By judging the end point address, the USB
  driver decides the transfer direction and sets up the PCI bus master read
  operation or write operation. For example, if the transfer direction is
  `EfiUsbDataIn`, the USB host controller reads from the DMA buffer. A bus master
  write operation is required.
* `Building the transfer context:` The _USB Specification_ defines several
  structures for a transfer. For example, Queue Head (QH) and Transfer
  Descriptor (TD) are special structures used to support the requirements of
  control, bulk, and interrupt transfers.

In this step, these QH and TD structures are created and linked to the Frame
List. One possible implementation can be creation of one QH and a list of TDs
to form a transfer list. The QH points to the first TD and occupies one entry
in the Frame List.
* `Executing the TD and getting the result:` The USB host controller
  automatically executes the TD when the timer expires. The UHCI driver waits
  until all of the TDs associated with the transfer are all completed. After
  that, the result of the TD execution is determined.
* `Cleaning up:` Delete the bulk transfer QH and TD structures from the Frame
  List, free related structures, and unmap the PCI DMA operation.

#### 19.1.4.2 Asynchronous transfer

The USB Host Controller Protocol provides the following two asynchronous
transfer services:
* `AsyncInterruptTransfer()`
* `AsyncIsochronousTransfer()`

To support asynchronous transfers, the USB host controller driver registers a
periodic timer event. Meanwhile, it maintains a queue for all asynchronous
transfers. When the timer event is signaled, the timer event callback function
evaluates this queue and checks to see if asynchronous transfers are now
complete.

Generally speaking, the main work of the timer event callback function is to go
through the asynchronous transfers queue. For each asynchronous transfer, it
checks whether an asynchronous transfer is completed or not and performs the
following:
* `If not completed:` The USB host controller driver takes no action and leaves
  the transfer on the queue.
* `If completed:` The USB host controller driver copies the data that it
  received to a predefined data buffer and removes the related QH and TD
  structures. It also invokes a preregistered transfer callback function. Based
  on that transfer's complete status, the USB host controller driver takes
  different additional actions such as:
  - If completed without error, update the transfer data status accordingly,
  e.g., data toggle bit.
  - If completed with error, it is suggested that the USB host controller do
  nothing and leave the error recovery work to the related USB device driver.

#### 19.1.4.3 Internal Memory Management

To implement USB transfers, the USB host controller driver manages many small
memory fragments as transfer data (i.e. QH and TD). If the USB host controller
driver uses the system memory management services to allocate these memory
fragments each time, then the overhead can be large. As a result, it is
recommended that the USB host controller driver manage these kinds of internal
memory usage itself. One possible implementation, as in EDK II, is that the
host controller driver can allocate a large buffer of memory in the Driver
Binding Protocol `Start()` service using UEFI memory services. The USB host
controller driver provides a small memory management algorithm to manage this
memory to satisfy internal memory allocations. By using this simple memory
management mechanism, it avoids the frequent system memory management calls.

#### 19.1.4.4 DMA

Most USB host controllers use DMA for their data transfer between host and
devices. Because the processor and USB host controller both access that
transfer data simultaneously, the USB host controller driver must use a common
buffer for all the memory that the host controller uses for data transfer. This
requirement means that the processor and the host controller have an identical
view of memory. See _Chapter_ _18_ for usage guidelines for managing PCI DMA
for common buffers.
