<!--- @file
  3.15.2 Connecting the PCI bus

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

### 3.15.2 Connecting the PCI bus

Platform initialization continues by loading the PCI bus driver. As the
driver's entry point is executed, the PCI bus driver installs the Driver
Binding Protocol and potentially the Component Name Protocols.

For example, the handle database as viewed with the `dh` UEFI Shell command
might look like the following after the PCI bus driver is loaded and started.
It contains one new driver image handle with the Loaded Image Protocol, Driver
Binding Protocol, and Component Name2 Protocol. Because this driver does follow
the UEFI Driver Model, no new controller handles are produced when the driver
is loaded and started. They are not produced until the driver is connected.

```
...
14: Image(PciBus) Driver Binding ComponentName
...
```

Later in the platform initialization process, UEFI-conformant firmware uses
`ConnectController()` to attempt to connect the PCI root bridge controller(s)
(handle #14 hex, as shown in the example above). The system has several priority rules for determining what driver to try first, but in this case it searches the handle database for driver handles (handles with the Driver Binding Protocol). The search finds handle #14 and call the Driver Binding Protocol `Supported()` service, passing in _controller handle \#14_. The PCI bus driver requires the Device Path Protocol and PCI Root Bridge
I/O Protocol to be started, so the `Supported()` service returns `EFI_SUCCESS`
when those two protocols are found on handle #14 After receiving `EFI_SUCCESS`
from the `Supported()` service, `ConnectController()` then calls the Driver
Binding Protocol `Start()` service with the same controller handle #14.

Due to the PCI Bus Driver, the `Start()` service uses the PCI Root Bridge I/O
Protocol functions to enumerate the PCI bus and discover all PCI devices. For
each PCI device/function that the PCI bus driver discovers, it creates a child
handle and installs an instance of the PCI I/O Protocol on the handle. The
handle is registered in the handle database as a "child" of the PCI root bridge
controller.

The PCI bus driver also copies the device path from the parent PCI root bridge device handle and appends a new PCI device path node Pci(Dev|Func). In cases where the PCI bus driver discovers a PCI-to-PCI bridge, the devices below the bridge are added as children to the bridge. In these cases, extra PCI device path nodes are added for each PCI-to-PCI bridge between the PCI root bridge and the PCI device.

For example, the handle database as viewed with the `dh` UEFI Shell command
might look like the following after the PCI bus driver is connected to the PCI
root bridge. It shows that:

* Nine PCI devices were discovered.
* The PCI device on handle #1B has an option ROM with a UEFI driver.
* That UEFI driver was loaded and executed and is shown as handle #1C.

Also notice that a single PCI card may have several UEFI handles if they have
multiple PCI functions.

<div style="page-break-after: always;"></div>

```
...
16: PciIo DevPath (Acpi(HWP0002,0,PNP0A03)/Pci(1|0))
17: PciIo DevPath (Acpi(HWP0002,0,PNP0A03)/Pci(1|1))
18: PciIo DevPath (Acpi(HWP0002,0,PNP0A03)/Pci(2|0))
19: PciIo DevPath (Acpi(HWP0002,0,PNP0A03)/Pci(2|1))
1A: PciIo DevPath (Acpi(HWP0002,0,PNP0A03)/Pci(2|2))
1B: PciIo DevPath (Acpi(HWP0002,0,PNP0A03)/Pci(3|0))
1C: Image(Acpi(HWP0002,0,PNP0A03)/Pci(3|0)) Driver Binding
1D: PciIo DevPath (Acpi(HWP0002,0,PNP0A03)/Pci(4|0))
1E: PciIo DevPath (Acpi(HWP0002,100,PNP0A03)/Pci(1|0))
1F: PciIo DevPath (Acpi(HWP0002,100,PNP0A03)/Pci(1|1))
...
```
