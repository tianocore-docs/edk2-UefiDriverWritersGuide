<!--- @file
  18.4 Accessing PCI resources

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

## 18.4 Accessing PCI resources

PCI drivers should only access the I/O and memory-mapped I/O resources on the
PCI controllers they manage. They should never attempt to access the I/O or
memorymapped I/O resource of a PCI controller that they are not managing. They
should also never touch the I/O or memory-mapped I/O resources of the chipset
or the motherboard.

The PCI I/O Protocol provides services that allow a PCI driver to easily access
the resources of the PCI controllers it is currently managing. These services
hide platformspecific implementation details and prevent a PCI driver from
inadvertently accessing resources of the motherboard or other PCI controllers.
The PCI I/O Protocol has also been designed to simplify the implementation of
PCI drivers. For example, a PCI driver should never read the BARs in the PCI
configuration header. Instead, the PCI driver passes in a _BarIndex_ and
_Offset_ into the PCI I/O Protocol services. The PCI bus driver is responsible
for managing the PCI controller's BARs.

The services of the PCI I/O Protocol allowing a PCI driver to access the
resources of a PCI controller include the following:

* `PciIo->PollMem()`

* `PciIo->PollIo()`

* `PciIo->Mem.Read()`

* `PciIo->Mem.Write()`

* `PciIo->Io.Read()`

* `PciIo->Io.Write()`

* `PciIo->Pci.Read()`

* `PciIo->Pci.Write()`

* `PciIo->CopyMem()`

Another important resource provided through the PCI I/O Protocol is the
contents of the PCI option ROM. The _RomSize_ and _RomImage_ fields of the PCI
I/O Protocol provide access to a copy of the PCI option ROM contents. These
fields may be useful if the PCI driver requires additional information from the
contents of the PCI option ROM.

**********
**Note:** _It is important that the PCI option ROM contents not be modified
through the_ _RomImage_ _field. Modifications to this buffer only modify the
copy of the PCI option ROM contents in system memory. The PCI I/O Protocol does
not provide services to modify the content of the actual PCI option ROM._
**********
