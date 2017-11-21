<!--- @file
  3.15.4 Console drivers

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

### 3.15.4 Console drivers

UEFI consoles drivers may include one or more of the following:

* Text console devices

* Graphical console devices

* Keyboards

* Mice

* Serial ports

Some systems may provide custom console devices. The following table shows
examples of console related UEFI Drivers from the EDK II. These UEFI Drivers
may be carried by the platform firmware or in standard containers for UEFI
Drivers such as PCI Option ROMs.

<div style="page-break-after: always;"></div>

###### Table 13-UEFI console drivers

| **Class of driver** | **Type of driver**        | **Driver name**                               | **Description and example**                                                                                  |
| ----------------- | --------------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| USB Console       | USB host controller driver  | UhciDxe br/><br/> EhciDxe br/><br/> XhciDxe   | Consumes the PCI I/O Protocol and produces the USB 2 Host Controller Protocol. <br/><br/> `25: Image(EhciDxe) DriverBinding ComponentName2 ComponentName` |
| USB Console       | USB bus driver              | UsbBusDxe           | Consumes the USB Host Controller 2 Protocol and produces the USB I/O Protocol. <br/><br/> `26: Image(UsbBusDxe)  DriverBinding  ComponentName2  ComponentName`    |
| USB Console       | USB keyboard driver         | UsbKbDxe            | Consumes the USB I/O Protocol and produces the Simple Input Ex Protocol and Simple Input Protocol. <br/><br/> `27: Image(UsbKbDxe) DriverBinding ComponentName2 ComponentName`   |
| USB Console       | USB mouse                   | UsbMouseDxe         | Consumes the USB I/O Protocol and produces the Simple Pointer Protocol. <br/><br/>  `28: Image(UsbMouseDxe) DriverBinding ComponentName2 ComponentName`       |
| Graphics          | Graphics Output             | CirrusLogic5430 Dxe | Consumes the PCI I/O Protocol and produces the Graphics Output Protocol.<br/><br/>  `2E: Image(CirrusLogic5430Dxe)  DriverBinding ComponentName2 ComponentName` |
| Graphics          | Graphics console driver     | GraphicsConsole Dxe | Consumes the Graphics Output Protocol and produces the Simple Text Output Protocol.  <br/><br/>  `2D: Image(GraphicsConsoleDxe)  ComponentName`      |
| Serial            | PCI Serial                  | PciSerialDxe        | Consumes the PCI I/O Protocol and produces the Serial I/O Protocol.  <br/><br/>  30: Image(PciSerialDxe) DriverBinding ComponentName2 ComponentName       |
| Serial            | Serial terminal driver      | TerminalDxe         | Consumes the Serial I/O Protocol and produces the Simple Text Input, Simple text Input Ex, and Simple Text Output Protocols.  <br/><br/> 31: Image(TerminalDxe) DriverBinding ComponentName2 ComponentName           |
| Generic Console   | Platform console management | ConPlatformDxe      | This driver is unique in that a single set of driver code produces two driver handles――one for the "Console Out" and another for the "Console In". This driver evaluates the set of physical console devices and the UEFI Console Variables that describe the platform settings for active consoles and marks the active consoles to they can be easily discovered by driver ConSplitterDxe. Different platforms may modify the default policy decisions this driver provides.  <br/><br/>  `32: Image(ConPlatformDxe) Driver  Binding ComponentName2 ComponentName`  <br/><br/>  `33: DriverBinding ComponentName2 ComponentName`      |
| Generic Console   | Console splitter driver     | ConSplitterDxe      | This driver may not be present on all platforms. It is only required on platforms that support multiple output console devices or multiple input console devices. It combines the various selected input and output devices for the following four basic UEFI user devices: <br/><br/> ConIn <br/><br/> ConOut <br/><br/> ErrOut <br/><br/> PointerIn <br/><br/> It also installs multiple driver handles for a single set of driver code. It installs driver handles to manage ConIn, ConOut, ErrOut, and PointerIn devices. The entry point of this driver creates virtual handles for ConIn, ConOut, and StdErr, respectively, that are called the following: <br/><br/> PrimaryConIn <br/><br/> PrimaryConOut <br/><br/> PrimaryStdErr <br/><br/>  The virtual handles always exist even if no console exists or no consoles are yet connected in the system.  <br/><br/> `34: Image(ConSplitterDxe) DriverBinding ComponentName2  ComponentName` <br/><br/> `35: DriverBinding ComponentName2 ComponentName`  <br/><br/> `36: DriverBinding ComponentName2 ComponentName` <br/><br/> `37: DriverBinding ComponentName2  ComponentName` <br/><br/> `38: TxtinEx Txtin SimplePointer AbsolutePointer` <br/><br/> `39: Txtout GraphicsOutput UgaDraw`   |
