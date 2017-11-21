<!--- @file
  3.6.1 Protocols are produced and consumed

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

### 3.6.1 Protocols are produced and consumed

Protocols enable inter-module communication in UEFI. To enable this
communication, one of the modules must create or "produce" the protocol. Other
modules (including drivers) may then use or "consume" the protocol.

Drivers are both consumers and producers of protocols. For example, a UEFI
Driver for a SCSI Host Controller on a PCI bus consumes the PCI I/O Protocol
and produces the SCSI Host Controller Protocols.

The initial producer of the protocol must "create" the protocol. The protocol
structure must be allocated from memory (allocated either statically in the
program or via a memory allocation operation). The protocol must then be
initialized by filling in its contents. This almost always involves filling in
the function pointers declared in the protocol structure. In other words, to
produce a protocol is to declare its functionality and publish that
functionality to the handle database (so other drivers can find and use that
declaration).

Although it is legal to store data in a protocol, this is `strongly discouraged` for data items that may change over time. It is not a safe way to store dynamic data. Instead, functions that provide get/set operations (as in object-oriented programming) are safer and more extensible. The producer then uses `InstallMultipleProtocolInterfaces()` (as defined in the Boot Service chapter of the _UEFI Specification_) or similar to install the protocol into the handle database and make the protocol available to others.

The consumer has a somewhat simpler task. The consumer looks up the protocol in
the handle database by GUID. With service protocols, for which there is only
one instance in the entire handle database, the consumer can use the
`LocateProtocol()` service. For protocols that may be present on multiple
handles in the handle database, the `LocateHandleBuffer()` service can be used
to locate the set of handles that support a specified protocol. The consumer
can then use the `OpenProtocol()` service to lookup a protocol on a specific
handle.

It is possible that the consumer is invoked before the producer. In this case,
the consumer can request it be notified when new instances of the protocol are
created. This is accomplished using the `RegisterProtocolNotify()` service.

Any UEFI image can use protocols during boot time. However, after
`ExitBootServices()` is called, the handle database is no longer available to
the image.

A complete description of all the services used to manage the handle database
and produce and consume protocols appears in _Chapter 5_.
