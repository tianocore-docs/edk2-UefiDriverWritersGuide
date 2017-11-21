<!--- @file
  Appendix B EDK II Sample Drivers

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

# Appendix B EDK II Sample Drivers

This appendix lists sample UEFI Drivers in the EDK II open source project along
with their UEFI Driver related properties. This is not an exhaustive list of
UEFI Drivers available from the EDK II open source project. Instead, a set of
UEFI Drivers that provide examples of each major UEFI Driver feature this guide
describes are listed. This appendix may be used to review UEFI Driver
implementations that implement a specific UEFI Driver feature. Or it may be
used to find an example UEFI Driver with a feature set that closely matches the
features required for a new UEFI Driver so an existing driver can be cloned as
a starting point.

<div style="page-break-after: always;"></div>

###### Table 47-UEFI Driver Properties

| **Field** | **Field value** | **Description**                                               |
| ------- | ------- | ----------------------------------------------------------------------- |
| DB      |         | Number of Driver Binding Protocols installed in the driver entry point. |
| CFG     | 1       | Driver Configuration Protocol is installed in the driver entry point.   |
|         | 2       | Driver Configuration 2 Protocol is installed in the driver entry point. |
|         | *       | Both Driver Configuration and Driver Configuration 2 are installed.     |
|         | H       | HII packages are installed in the driver entry point for configuration. |
| DIAG    | 1       | Driver Diagnostics Protocol is installed in the driver entry point.     |
|         | 2       | Driver Diagnostics 2 Protocol is installed in the driver entry point.   |
|         | *       | Both Driver Diagnostics and Driver Diagnostics 2 are installed.         |
| CN      | 1       | Component Name Protocol is installed in the driver entry point.         |
|         | 2       | Component Name 2 Protocol is installed in the driver entry point.       |
|         | *       | Both Component Name and Component Name 2 are installed.                 |
| Class   | B       | Bus driver.                                                             |
|         | D       | Device driver.                                                          |
|         | H       | Hybrid driver.                                                          |
|         | R       | Root bridge driver.                                                     |
|         | S       | Service driver.                                                         |
|         | I       | Initializing driver.                                                    |
| Child   | All     | All child handles in first call to Start().                             |
|         | 1/All   | Can create 1 child handle at a time or all child handles in Start().    |
|         | 1       | Creates at most 1 child handle in Start().                              |
|         | 0       | Create no child handles in Start(). Used for hot-plug bus types.        |
| Parent  |         | Number of parent drivers to this driver                                 |
| `Field` | `Field` | `Description`                                                           |
|         | `value` |                                                                         |
| Type    | B       | UEFI Boot Services Driver. UEFI Runtime Driver.                         |
|         | R       |                                                                         |
| UL      | Y       | Driver is unloadable.                                                   |
| HP      | Y       | Driver supports a Hot Plug device or bus                                |

<div style="page-break-after: always;"></div>

###### Table 48-Sample UEFI Driver Properties

| **Driver** |**DB**|**CFG**|**DIAG**|**CN**|**CLASS**|
| ------------------------------------------------- | --- | ----- | --- | ----- | --- |
| IntelFrameworkModulePkg/Bus/Isa/ `IsaBusDxe`      | 1   | -     | -   | *     | B   |
| IntelFrameworkModulePkg/Bus/Isa/ `IsaFloppyDxe`   | 1   | -     | -   | *     | D   |
| IntelFrameworkModulePkg/Bus/Isa/ `IsaSerialDxe`   | 1   | -     | -   | *     | B   |
| IntelFrameworkModulePkg/Bus/Isa/ `Ps2KeyboardDxe` | 1   | -     | -   | *     | D   |
| IntelFrameworkModulePkg/Bus/Isa/ `Ps2MouseDxe`    | 1   | -     | -   | *     | D   |
| MdeModulePkg/Bus/Pci/`PciBusDxe`                  | 1   | -     | -   | *     | B   |
| DuetPkg/`PciBusNoEnumerationDxe`                  | 1   | -     | -   | *     | B   |
| MdeModulePkg/Bus/Pci/`UhciDxe`                    | 1   | -     | -   | *     | D   |
| MdeModulePkg/Bus/Pci/`EhciDxe`                    | 1   | -     | -   | *     | D   |
| MdeModulePkg/Bus/Pci/`XhciDxe`                    | 1   | -     | -   | *     | D   |
| OptionRomPkg/`UndiRuntimeDxe`                     | 1   | -     | -   | -     | B   |
| OptionRomPkg/`CirrusLogic5430Dxe`                 | 1   | -     | -   | *     | D   |
| MdeModulePkg/Bus/Scsi/`ScsiBusDxe`                | 1   | -     | -   | *     | B   |
| MdeModulePkg/Bus/Scsi/`ScsiDiskDxe`               | 1   | -     | -   | *     | D   |
| MdeModulePkg/Bus/Ata/ `AtaAtapiPassThruDxe`       | 1   | -     | -   | *     | D   |
| MdeModulePkg/Bus/Ata/`AtaBusDxe`                  | 1   | -     | -   | *     | B   |
| MdeModulePkg/Bus/Usb/`UsbBusDxe`                  | 1   | -     | -   | *     | B   |
| MdeModulePkg/Bus/Usb/`UsbKbDxe`                   | 1   | -     | -   | *     | D   |
| MdeModulePkg/Bus/Usb/ `UsbMassStorageDxe`         | 1   | -     | -   | *     | D   |
| MdeModulePkg/Bus/Usb/`UsbMouseDxe`                | 1   | -     | -   | *     | D   |
| IntelFrameworkModulePkg/Bus/Pci/ `IdeBusDxe`      | 1   | 1     | *   | *     | H   |
| FatPkg/`EnhancedFatDxe`                           | 1   | -     | -   | *     | D   |
| MdeModulePkg/Universal/Console/ `ConPlatfomDxe`   | 2   | -     | -   | *     | D   |
| MdeModulePkg/Universal/Console/ `ConSplitterDxe`  | 4   | -     | -   | *     | B   |
| MdeModulePkg/Universal/Console/ `TerminalDxe`     | 1   | -     | -   | *     | H   |
| MdeModulePkg/Universal/`EbcDxe`                   | -   | -     | -   | -     | S   |
| PcAtChipsetPkg/`PciHostBridgeDxe`                 | -   | -     | -   | -     | R   |
| MdeModulePkg/Universal/Network/ `Ip4ConfigDxe`    | 1   | H     | -   | *     | H   |
| MdeModulePkg/Universal/ `HiiResourcesSampleDxe`   | -   | H     | -   | -     | S   |

<div style="page-break-after: always;"></div>

| **Driver** |**CHILD**|**PARENT**|**TYPE**|**UL**|**HP**|
| ------------------------------------------------- | --- | --- | ----- | --- | --- |
| IntelFrameworkModulePkg/Bus/Isa/ `IsaBusDxe`      | All | 1   | B     | -   | -   |
| IntelFrameworkModulePkg/Bus/Isa/ `IsaFloppyDxe`   | -   | 1   | B     | -   | -   |
| IntelFrameworkModulePkg/Bus/Isa/ `IsaSerialDxe`   | 1   | 1   | B     | -   | -   |
| IntelFrameworkModulePkg/Bus/Isa/ `Ps2KeyboardDxe` | -   | 1   | B     | -   | -   |
| IntelFrameworkModulePkg/Bus/Isa/ `Ps2MouseDxe`    | -   | 1   | B     | -   | -   |
| MdeModulePkg/Bus/Pci/`PciBusDxe`                  | 1/All | 1   | B     | -   | Y   |
| DuetPkg/`PciBusNoEnumerationDxe`                  | 1/All | 1   | B     | -   | -   |
| MdeModulePkg/Bus/Pci/`UhciDxe`                    | -   | 1   | B     | -   | -   |
| MdeModulePkg/Bus/Pci/`EhciDxe`                    | -   | 1   | B     | -   | -   |
| MdeModulePkg/Bus/Pci/`XhciDxe`                    | -   | 1   | B     | -   | -   |
| OptionRomPkg/`UndiRuntimeDxe`                     | 1   | 1   | R     | -   | -   |
| OptionRomPkg/`CirrusLogic5430Dxe`                 | -   | 1   | B     | -   | -   |
| MdeModulePkg/Bus/Scsi/`ScsiBusDxe`                | 1/All  | 1   | B     | -   | -   |
| MdeModulePkg/Bus/Scsi/`ScsiDiskDxe`               | -   | 1   | B     | -   | -   |
| MdeModulePkg/Bus/Ata/ `AtaAtapiPassThruDxe`       | -   | 1   | B     | -   | -   |
| MdeModulePkg/Bus/Ata/`AtaBusDxe`                  | 1/All | 1   | B     | -   | -   |
| MdeModulePkg/Bus/Usb/`UsbBusDxe`                  | 0   | 1   | B     | -   | Y   |
| MdeModulePkg/Bus/Usb/`UsbKbDxe`                   | -   | 1   | B     | -   | -   |
| MdeModulePkg/Bus/Usb/ `UsbMassStorageDxe`         | -   | 1   | B     | -   | -   |
| MdeModulePkg/Bus/Usb/`UsbMouseDxe`                | -   | 1   | B     | -   | -   |
| IntelFrameworkModulePkg/Bus/Pci/ `IdeBusDxe`      | 1/All  | 1   | B     | -   | -   |
| FatPkg/`EnhancedFatDxe`                           | -   | 1   | B     | Y   | -   |
| MdeModulePkg/Universal/Console/ `ConPlatfomDxe`   | -   | 1   | B     | -   | -   |
| MdeModulePkg/Universal/Console/ `ConSplitterDxe`  | All | >1   | B     | -   | -   |
| MdeModulePkg/Universal/Console/ `TerminalDxe`     | 1   | 1   | B     | -   | -   |
| MdeModulePkg/Universal/`EbcDxe`                   | -   | -   | B     | -   | -   |
| PcAtChipsetPkg/`PciHostBridgeDxe`                 | -   | -   | B     | -   | -   |
| MdeModulePkg/Universal/Network/ `Ip4ConfigDxe`    | All | 1   | B     | Y   | -   |
| MdeModulePkg/Universal/ `HiiResourcesSampleDxe`   | -   | -   | B     | Y   | -   |
