<!--- @file
  2 UEFI Driver Implementation Checklist

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

# 2 UEFI Driver Implementation Checklist

The following is a checklist for implementing good, conformant, and efficient
UEFI Drivers. References to sections of the guide that apply to each of the
items in the checklist are provided so a UEFI Driver developer can easily
determine the sections of the guide that apply to a specific UEFI Driver
development task. The terminology used in this checklist is introduced in
_Chapter 3_.

When possible, copy an existing UEFI Driver with similar features and modify it
to match the new UEFI Driver requirements. _Appendix B_ contains a table of
UEFI Drivers and features that each implements to help select an existing UEFI
Driver.

Some UEFI drivers are ported from PC BIOS legacy option ROMs or EFI/UEFI
Drivers based on previous releases of the _EFI/UEFI Specification_. While
porting a driver from one environment to another is often done to save time and
leverage resources, note that it requires careful attention to detail. Without
a complete understanding of the target environment, the final driver can have
remnants of the previous design that may degrade performance and functionality
in the new environment.

1. Determine UEFI Driver Type
  * UEFI Driver Model (_Section 3.10_ and _Chapter 6_)
    - Must produce Driver Binding Protocol (_Chapter 9_)
        + Device Driver (_Section 6.1_, _Section 7.2_, and _Chapter 9_)
        + Bus Driver (_Section 6.2_, _Section 7.2_, and _Chapter 9_)
        + Hybrid Drive (_Section 6.3_, _Section 7.2_, and _Chapter 9_)
    - Determine Optional UEFI Driver Model Features
    - Component Name 2 Protocol (_Section 7.1_, _Section 7.2_, _Chapter 11_)
    - Component Name Protocol (_Section 7.1_, _Section 7.2_, _Chapter 11_)
    - Driver Family Override Protocol (_Section 7.2.4_ and _Chapter 15_)
    - Driver Diagnostics 2 Protocol (_Section 7.1_, _Section 7.2_, _Chapter 13_)
    - HII Packages (_Section 7.1_, _Section 7.4_, and _Chapter 12_)
    - HII Config Access Protocol (_Section 7.1_, _Section 7.5_, and _Chapter 12_)
    - Driver Configuration 2 Protocol (_Section 7.1_ and _Chapter 12_)
    - Driver Configuration Protocol (_Section 7.1_ and _Chapter 12_)
    - Driver Health Protocol (_Section 7.1_, _Section 7.2.3_, _Chapter 14_)
    - Bus Specific Driver Override Protocol (_Chapter 17_)
  - Service Binding Protocol (_Chapter 10_)
  * Service Driver (_Section 6.4_ and   _Section 7.9_)
  * Root Bridge Driver (_Section 6.5_ and _Section 7.10_)
  * Initializing Driver (_Section 6.6_ and _Section 7.8_)

2. Determine Optional UEFI Driver Features
   * Install an `Unload()` handler (_Section 7.6_ and _Section 5.2.1.2_)
   * HII Packages (_Section 7.1_, _Section 7.4_, and _Chapter 12_)
   * HII Config Access Protocol (_Section 7.1_, _Section 7.5_, and _Chapter 12_)
   * Driver Supported EFI Version Protocol (_Chapter 6_, _Section 7.3_, _Chapter 16_)
     - Required for all plug in cards
   * Service Binding Protocol (_Chapter 10_)

3. Identify the required UEFI supported CPU architectures
   * IA32 (_Chapter 4_)
   * X64 (_Chapter 4_)
   * IPF (_Chapter 4_ and _Chapter 28_)
   * EFI Byte Code (_Chapter 4_, _Section 4.4_, _Section 18.6_, and _Chapter 29_)

4. Identify consumed I/O protocols
   * PCI I/O Protocol to access a PCI Controller (_Chapter 18_)
     - Always call `PciIo->Attributes()` (_Section 18.3.2_)
       + Advertises dual address cycle capability
       + Save and enable attributes in Start()
       + Disable attributes in Stop()
     - DMA-Bus master write operations (_Section 18.5_)
   + Must call `PciIo->Flush()`
     - DMA-Setting up with `PciIo->Map()` (_Section 18.5_)
       + Do not use returned device address
       + Not all chipsets have 1:1 bus/system mappings
     - PCI Option ROM (_Section 18.7_)
   + USB I/O Protocol to access a USB (_Chapter 19_) Device
   + SCSI I/O Protocol to access a SCSI Device (_Chapter 20_)
   + ATA Pass Thru Protocol to access a SATA Device (_Chapter 21_)

5. Identify the boot related protocol(s) the UEFI Driver must produce
   * Keyboard (_Section 22.2_)
   * Mouse
   * Tablet
   * Text Console (_Section 22.3_)
   * Serial Port (_Section 22.4_)
   * Graphics Console (_Chapter 23_)
   * Mass Storage (_Chapter 24_)
   * Network Controller (_Chapter 25_)
   * Load File Protocol (_Chapter 27_)
   * User Credential Provider (_Chapter 26_)
   * USB Host Controller (_Section 19.1_)
   * SCSI Host Controller (_Section 20.1_)
   * ATA Host Controller (_Section 21.1_ and _Section 20.1_)

6. Build UEFI Driver (_Chapter 30_)

7. Test and Debug UEFI Driver (_Chapter 31_)
   * Use UEFI Shell to load and exercise functionality
   * Test all produced protocols
   * Test on multiple platforms
   * Pass UEFI SCT tests for the devices the UEFI Driver manages
