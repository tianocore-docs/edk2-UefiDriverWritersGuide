<!--- @file
  Appendix C Glossary

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

# Appendix C Glossary

The following table defines terms used in this document. See the glossary in
the _UEFI Specification_ for definitions of additional terms.

###### Table 49-Definitions of terms

| **Term**            | **Definition**                                                   |
| ------------------- | ---------------------------------------------------------------- |
| '<Enumerated Type>' | Element of an enumeration. Type INTN.
| ACPI                | Advanced Configuration and Power Interface.
| ANSI                | American National Standards Institute.
| API                 | Application programming interface.
| ASCII               | American Standard Code for Information Interchange.
| ATAPI               | Advanced Technology Attachment Packet Interface.
| BAR                 | Base Address Register.
| BBS                 | BIOS Boot Specification.
| BC                  | Base Code.
| BEV                 | Bootstrap Entry Vector. A pointer that points to code inside an option ROM that directly loads an OS.
| BIOS                | Basic input/output system.
| BIS                 | Boot Integrity Services.
| BM                  | Boot manager.
| BOOLEAN             | Logical Boolean. 1-byte value containing a 0 for `FALSE` or a 1 for `TRUE`. Other values are undefined.
| BOT                 | Bulk-Only Transport.
| BS                  | EFI boot services table or EFI Boot Service(s).
| CBI                 | Control/Bulk/Interrupt Transport.
| CBW                 | Command Block Wrapper.
| CHAR16              | 2-byte character. Unless otherwise specified, all strings are stored in the  UTF-16 encoding format as defined by Unicode 2.1 and ISO/IEC 10646 standards.
| CHAR8               | 1-byte character.
| CID                 | Compatible ID.
| CONST               | Declares a variable to be of type const. This modifier is a hint to the compiler to enable optimization and stronger type checking at compile time. |
| CR                  | Containing Record.
| CRC                 | Cyclic Redundancy Check.
| CSW                 | Command Status Wrapper.
| DAC                 | Dual Address Cycle.
| DHCP4               | Dynamic Host Configuration Protocol Version 4.
| DID                 | Device ID.
| DIG64               | Developer's Interface Guide for 64-bit Intel Architecture-based Servers.
| DMA                 | Direct Memory Access.
| EBC                 | EFI Byte Code.
| ECR                 | Engineering Change Request.
| EFI                 | Extensible Firmware Interface.
| EFI_EVENT           | Handle to an event structure. Type VOID *.
| EFI_GUID            | 128-bit buffer containing a unique identifier value. Unless otherwise specified, aligned on a 64-bit boundary.
| EFI_HANDLE          | A collection of related interfaces. Type VOID *.
| EFI_IP_ADDRESS      | 16-byte buffer aligned on a 4-byte boundary. An IPv4 or IPv6 internet protocol address.
| EFI_Ipv4_ADDRESS    | 4-byte buffer. An IPv4 internet protocol address.
| EFI_Ipv6_ADDRESS    | 16-byte buffer. An IPv6 internet protocol address.
| EFI_LBA             | Logical block address. Type UINT64.
| EFI_MAC_ADDRESS     | 32-byte buffer containing a network Media Access Controller address.
| EFI_STATUS          | Status code. Type INTN.                                                                                                                             |
| EFI_TPL             | Task priority level. Type UINTN.                                                                                                                    |
| EISA                | Extended Industry Standard Architecture.                                                                                                            |
| FAT                 | File allocation table.                                                                                                                              |
| FIFO                | First In First Out.                                                                                                                                 |
| FPSWA               | Floating Point Software Assist.                                                                                                                     |
| FRU                 | Field Replaceable Unit.                                                                                                                             |
| FTP                 | File Transfer Protocol.                                                                                                                             |
| GPT                 | Guided Partition Table.                                                                                                                             |
| GUID                | Globally Unique Identifier.                                                                                                                         |
| HC                  | Host controller.                                                                                                                                    |
| HID                 | Hardware ID.                                                                                                                                        |
| I/O                 | Input/output.                                                                                                                                       |
| IA32                | 32-bit Intel architecture.                                                                                                                          |
| IBV                 | Independent BIOS vendor.                                                                                                                            |
| IDE                 | Integrated Drive Electronics.                                                                                                                       |
| IEC                 | International Electrotechnical Commission.                                                                                                          |
| IHV                 | Independent hardware vendor.                                                                                                                        |
| IN                  | Datum is passed to the function.                                                                                                                    |
| INT                 | Interrupt.                                                                                                                                          |
| INT16               | 2-byte signed value.                                                                                                                                |
| INT32               | 4-byte signed value.                                                                                                                                |
| INT64               | 8-byte signed value.                                                                                                                                |
| INT8                | 1-byte signed value.                                                                                                                                |
| INTN                | Signed value of native width. (4 bytes on IA32, 8 bytes on X64 and IPF)                                                                             |
| IPF                 | Itanium processor family.                                                                                                                           |
| Ipv4                | Internet Protocol Version 4.                                                                                                                        |
| Ipv6                | Internet Protocol Version 6.                                                                                                                        |
| ISA                 | Industry Standard Architecture.                                                                                                                     |
| ISO                 | Industry Standards Organization.                                                                                                                    |
| iSCSI               | SCSI protocol over TCP/IP.                                                                                                                          |
| KB                  | Keyboard.                                                                                                                                           |
| LAN                 | Local area network.                                                                                                                                 |
| LUN                 | Logical Unit Number.                                                                                                                                |
| MAC                 | Media Access Controller.                                                                                                                            |
| MMIO                | Memory Mapped I/O.                                                                                                                                  |
| NIC                 | Network interface controller.                                                                                                                       |
| NII                 | Network Interface Identifier.                                                                                                                       |
| NVRAM               | Nonvolatile RAM.                                                                                                                                    |
| OEM                 | Original equipment manufacturer.                                                                                                                    |
| OHCI                | Open Host Controller Interface.                                                                                                                     |
| OpROM               | Option ROM.                                                                                                                                         |
| OPTIONAL            | Datum that is passed to the function is optional, and a `NULL` may be passed if the value is not supplied.                                          |
| OS                  | Operating system.                                                                                                                                   |
| OUT                 | Datum is returned from the function.                                                                                                                |
| PCI                 | Peripheral Component Interconnect.                                                                                                                  |
| PCMCIA              | Personal Computer Memory Card International Association.                                                                                            |
| PE                  | Portable Executable.                                                                                                                                |
| PE/COFF             | PE32, PE32+, or Common Object File Format.                                                                                                          |
| PNPID             | Plug and Play ID.                                                                                                           |
| POST              | Power On Self Test.                                                                                                         |
| PPP               | Point-to-Point Protocol.                                                                                                    |
| PUN               | Physical Unit Number.                                                                                                       |
| PEI               | Pre-boot Execution Environment.                                                                                             |
| PXE BC (or PxeBc) | PXE Base Code Protocol.                                                                                                     |
| QH                | Queue Head.                                                                                                                 |
| RAID              | Redundant Array of Inexpensive Disks.                                                                                       |
| RAM               | Random access memory.                                                                                                       |
| ROM               | Read-only memory.                                                                                                           |
| RT                | EFI Runtime Table and EFI Runtime Service(s).                                                                               |
| SAL               | System Abstraction Layer.                                                                                                   |
| SCSI              | Small Computer System Interface.                                                                                            |
| SIG               | Special Interest Group.                                                                                                     |
| S.M.A.R.T.        | Self-Monitoring Analysis Reporting Technology.                                                                              |
| SMBIOS            | System Management BIOS.                                                                                                     |
| SMBus             | System Management Bus.                                                                                                      |
| SNP               | Simple Network Protocol.                                                                                                    |
| SPT               | SCSI Pass Thru.                                                                                                             |
| ST                | EFI System Table                                                                                                            |
| STATIC            | The function has local scope. This modifier replaces the standard C static key word, so it can be overloaded for debugging. |
| TCP/IP            | Transmission Control Protocol/Internet Protocol.                                                                            |
| TD                | Transfer Descriptor.                                                                                                        |
| TPL               | Task Priority Level.                                                                                                        |
| UART              | Universal Asynchronous Receiver-Transmitter.                                                                                |
| UHCI              | Universal Host Controller Interface.                                                                                        |
| UID               | Unique ID.                                                                                                                  |
| UINT16            | 2-byte unsigned value.                                                                                                      |
| UINT32            | 4-byte unsigned value.                                                                                                      |
| UINT64            | 8-byte unsigned value.                                                                                                      |
| UINT8             | 1-byte unsigned value.                                                                                                      |
