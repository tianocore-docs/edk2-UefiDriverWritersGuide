<!--- @file
  3.15.7 ConOut

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

### 3.15.7 ConOut

As with _ConIn_, firmware connects the _ConOut_ devices using the device paths
in the _ConOut_ global UEFI variable. If _ConIn_ was not complicated enough, the
_ConOut_ global UEFI device path in this example is a compound device path and
indicates that the _ConOut_ device is being mirrored with the console splitter
driver to two separate devices.

```
  ConOut = Acpi(HWP0002,0,PNP0A03)/Pci(1|1)/Uart(9600 N81)/VenMsg(Vt100+);Acpi(HWP0002,0,PNP0A03)/Pci(4|0)
```

The UEFI connection process searches the handle database for a device path that
matches the first device path in the _ConOut_ variable:

```
Acpi(HWP0002,0,PNP0A03)/Pci(1|1)/Uart(9600 N81)/VenMsg(Vt100+)`
```

Luckily, the device path already exists on handle 3C in its entirety thanks to
the connection work done for _ConIn_.

```
  3C: Txtin Txtout ConIn ConOut StdErr DevPath
  (Acpi(HWP0002,0,PNP0A03)/Pci(1|1)/Uart(9600 N81)/VenMsg(Vt100+))
```

UEFI performs a `ConnectController()` on handle 3C. Because this step was
previously done with _ConIn_, there is nothing more to be done here.

The connection process has not yet been completed for _ConOut_ because the
device path is a compound device path and a second device needs to be connected:

```
  Acpi(HWP0002,0,PNP0A03)/Pci(4|0)
```

The UEFI connection process searches the handle database for a device path that matches Acpi(HWP0002,0,PNP0A03)/Pci(4|0). The device path already exists in its entirety on handle 1C and was created by the PCI bus driver when it started and exposed the PCI devices.

```
  1C: PciIo DevPath (Acpi(HWP0002,0,PNP0A03)/Pci(4|0))
```

UEFI now performs a `ConnectController()` on handle 1C. Note that the device
path is a complete match, so there is no remaining device path to pass in this
time. `ConnectController()` constructs the prioritized list of drivers in the system
and calls the `Supported()` service for each one, passing in the device handle
1C. The only driver that returns `EFI_SUCCESS` is the GraphicsOutput driver.

```
  2E: Image(CirrusLogic5430Dxe) Driver Binding ComponentName2 ComponentName
```

`ConnectController()` calls this driver's `Start()` function and `Start()`
consumes the device's `EFI_PCI_IO_PROTOCOL` and installs the
`EFI_GRAPHICS_OUTPUT_PROTOCOL` onto the _device handle_ 1C.

```
  1C: PciIo GraphicsOutput DevPath (Acpi(HWP0002,0,PNP0A03)/ Pci(4|0))
```

`ConnectController()` continues to process its list of drivers and finds that
the `GraphicsConsole` driver's `Supported()` service returns `EFI_SUCCESS`.

```
  2D: Image(GraphicsConsoleDxe) DriverBinding ComponentName2 ComponentName
```

Next, the graphics console driver's `Start()` service consumes the 
`EFI_GRAPHICS_OUTPUT_PROTOCOL` and produces the `EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL` 
on the same _device handle_ 1C.

```
  1C: Txtout PciIo GraphicsOutput DevPath (Acpi(HWP0002,0,PNP0A03)/
Pci(4|0))
```

`ConnectController()` continues to process its list of drivers, now searching
for a driver that supports this controller, and finds two driver handles that
return `EFI_SUCCESS` from their `Supported()` services. These two driver
handles are from the platform console management driver:

```
  32: Image(ConPlatformDxe) DriverBinding ComponentName2 ComponentName
```

Driver handle 32 installs a _ConOut_ Tag GUID on the handle if the device path
is listed in the _ConOut_ global UEFI variable. In this example, the case is
true. Driver 32 also installs a _StdErr_ Tag GUID on the handle if the device
path is listed in the _ErrOut_ global UEFI variable. This case is also true in
the example. Therefore, handle 1C has two new protocols on it: _ConOut_ and
_StdErr_.

```
  1C: Txtout PciIo ConOut StdErr DevPath (Acpi(HWP0002,0,PNP0A03)/Pci(4|0))
```

These two protocols (_ConOut_ and _StdErr_) are used to mark devices in the
system that have been user-selected as _ConOut_ and _StdErr_. These protocols
are actually just Tag GUID without any functions or data.

There are two other driver handles that return `EFI_SUCCESS` to the
`Supported()` service. These driver handles are from the console splitter
driver for the _ConOut_ and _StdErr_ devices in the system.

```
  36: DriverBindingComponentName
  37: DriverBindingComponentName
```

Remember that when the console splitter driver was first loaded, it created
three virtual handles.

```
  38: TxtinEx TxtIn SimplePointer AbsolutePointer
  39: Txtout GraphicsOutput UgaDraw
  3A: Txtout
```

The console splitter driver's `Supported()` _service_ for driver handle 36
examines the handle 1C for a _ConOut_ Protocol. Having found it, `EFI_SUCCESS`
is returned. The `Start()` service then opens the _ConOut_ protocol on _device
handle_ 1C such that the _device handle_ 39 becomes a child controller and
starts aggregating the `SIMPLE_TEXT_OUTPUT_PROTOCOL` services.

The same thing happens for _driver handle_ 37 with _StdErr_; the `SIMPLE_TEXT_OUTPUT_PROTOCOL` functionality on _device handle_ 1C is aggregated into the `SIMPLE_TEXT_OUTPUT_PROTOCOL` on _device handle_ 3A.
