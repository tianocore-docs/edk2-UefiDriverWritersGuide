<!--- @file
  23.2.3 Driver Binding Protocol Implementation

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

### 23.2.3 Driver Binding Protocol Implementation

Like all drivers that follow the UEFI driver model, the image entry point of a
graphics driver installs the Driver Binding Protocol instance on the image
handle. The driver must implement all three of the services in the Driver
Binding Protocol-`Supported()`, `Start()`, and `Stop()`.

The `Supported()` function tests to see whether the given handle is a
manageable adapter. The driver should check that `EFI_DEVICE_PATH_PROTOCOL` and
`EFI_PCI_IO_PROTOCOL` are present to ensure the handle that is passed in
represents a PCI device. Then the driver should verify that the device is
conformant and manageable by reading the _ClassCode_, _VendorId_, and
_DeviceId_ from the device's PCI configuration header.

The `Start()` function tells the Graphics driver to start managing the
controller. In this function, a Graphics driver should use chip-specific
knowledge to do the following:

1. Initialize the adapter.

2. Enable the PCI device.

3. Allocate resources.

4. Construct data structures for the driver to use (if required by the device).

5. Enumerate the outputs that are enabled on the device.

6. Create child handles for each detected (and enabled) physical output
   (physical child handles) and install EFI_DEVICE_PATH_PROTOCOL.

7. Get EDID information from each physical output device connected and install
   EFI_EDID_DISCOVERED_PROTOCOL on the child handle.

8. Create child handles for each valid combination of 2 or more video outputs
   (logical child handles) and install EFI_DEVICE_PATH_PROTOCOL.

9. Check RemainingDevicePath to see if the correct child or children were
   created or if NULL select a default set. If incorrect children (no defaults)
   clean up memory and return EFI_UNSUPPORTED. If default or correct children
   set them active.

10. Call GetEdid() function to check for overrides on each active physical
    child handle and produce EFI_EDID_ACTIVE_PROTOCOL on each child protocol
    based on the result.

11. Install EFI_GRAPHICS_OUTPUT_PROTOCOL on each active child handle (physical
    or logical).

12. Install the EFI_COMPONENT_NAME_PROTOCOL and EFI_COMPONENT_NAME2_PROTOCOL.

13. In order to support faster boot times, a default mode set and clear screen
    operation must not be performed in the Start() function. This allows the
    UEFI Boot Manager to select the best mode for the current boot scenario and
    set the mode one time.

The `Start()` function should not scan for devices every time the driver is
started. It should depend on `RemainingDevicePath` parameter to determine what
to start. Only if `NULL` was passed in should the driver should create a device
handle for each device that was found in the scan behind the controller.
Otherwise the driver should only start what was specified in
`RemainingDevicePath`.

The `Stop()` function performs the opposite operations as `Start()`. Generally
speaking, a Graphics driver is required to do the following:

1. Uninstall all protocols on all child handles and close all the child handles.

2. Uninstall all protocols that were attached on the host controller handle.

3. Close all protocol instances that were opened in the Start() function.

4. Release all resources that were allocated for this driver.

5. Disable the adapter.

In general, if it is possible to support RemainingDevicePath, the driver should
do so to support the rapid boot capability in the UEFI driver model.
