<!--- @file
  31.3.5 Connecting UEFI Drivers

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

### 31.3.5 Connecting UEFI Drivers

The table below lists the UEFI Shell commands that can be used to test the
connecting of UEFI drivers to devices. There command support many flags, so
only a few are shown in the table below.

###### Table 44-UEFI Shell commands for connecting UEFI drivers

| **Command**  | **Description**                                                                |
| ------------ | ------------------------------------------------------------------------------ |
| Connect      | Can be used to connect all UEFI drivers to all devices in the system or connect UEFI drivers to a single device.  |
| Disconnect | Stops UEFI drivers from managing a device.    |
| Reconnect  | Is the equivalent of executing the `Disconnect` and `Connect` commands back to back. The `Reconnect` command is the best command for testing the Driver Binding Protocol of UEFI drivers. This command tests the  `Supported()`, `Start()`, and `Stop()` services of the Driver Binding Protocol. The `Reconnect -r` command tests the Driver Binding Protocol for every UEFI driver that follows the UEFI driver model. Use this command before a UEFI driver is loaded to verify that the current set of drivers pass the `Reconnect -r` test, and then load the new UEFI driver and rerun the `Reconnect -r` test. A UEFI driver is not complete until it passes this interoperability test with the UEFI core and the full set of UEFI drivers at least 3 times in a row. |

#### 31.3.5.1 Connect

This UEFI Shell command requests UEFI drivers to start managing a device. This
command tests the Driver Binding Protocol `Supported()` and `Start()` functions
in the driver that has the specified handle. The `Start()` function may create
new child handles if the UEFI Driver is a bus driver or a hybrid driver.

The `Connect` command can be used to connect all UEFI drivers to all devices in
the system or connect UEFI drivers to a single device. Here are several
examples of using the `Connect` command:

* Example 1: Connects all drivers to all
devices:
  `fs0:> Connect -r`

* Example 2: Connects all drivers to the device that is
abstracted by handle 23:
  `fs0:> Connect 23`

* Example 3: Connects the UEFI driver on handle 27 to the device on handle 23:
  `fs0:> Connect 23 27`

In Example 3, note that there is a handle for the driver and a handle for the
hardware device. The `Connect` command makes the connection between the two
handles. The `Start()` service associates the driver with the specified
hardware. If the driver needs to create a child handle for the device, it does
so as part of its `Start()` function. Although the handles cannot be known
until the driver is executed, the handle database can be evaluated to determine
the handle numbers that are passed to the connect command.

#### 31.3.5.2 Disconnect

The `Disconnect` UEFI command stops UEFI drivers from managing a device. This
command tests the Driver Binding Protocol `Stop()` function in the driver.

This UEFI Shell command does not allow a driver to be disconnected unless all
the child handles associated with that driver are destroyed first. Basically,
this UEFI Shell command does not allow any orphans to be left in the system.

**********
**TIP:** When disconnecting drivers one at a time, begin at the lowest level of
child handle and work up the device tree one node at a time. The UEFI Shell
command **devtree** provides a device tree view.
**********

The code below shows the following examples of using the `Disconnect` command:

**********
**Caution:** The **Disconnect** command supports a -r switch that can be used
without any other parameters. Do NOT use this mode of the Disconnect command
because it will disconnect all UEFI Drivers from all devices in the entire
platform which typically includes the consoles devices.
**********

* Example 1: Disconnects all the UEFI drivers from the device represented by
handle 23:

  `fs0:> Disconnect 23`

* Example 2: Disconnects all UEFI drivers on handle 23 and the child process (27)
which was created by that driver:

  `fs0:> Disconnect 23 27`

* Example 3: Disconnects the UEFI driver represented by handle 29 The UEFI driver
on handle 29 produced a child (32) and is managing a device (44), which has a
device path associated with it. In order to disconnect the driver, the child
and the device path managed by that driver are destroyed along with stopping
the driver.

  `fs0:> Disconnect 29 32 44`

#### 31.3.5.3 Reconnect

The code below shows the following examples of the `Reconnect` command:

* Example 1: Reconnects all the UEFI drivers to the device handle 23:

  `fs0:> Reconnect 23`

* Example 2: Reconnects the UEFI driver on handle 27 to the device on handle 23:

  `fs0:> Reconnect 23 27`

* Example 3: Reconnects all the UEFI drivers in the system:

  `fs0:> Reconnect -r`
