<!--- @file
  31.3.6 Driver and Device Information

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

### 31.3.6 Driver and Device Information

The _following table_ lists the UEFI Shell commands that can be used to dump
information about the UEFI Drivers that follow the UEFI Driver Model. Each of
these commands shows information from a slightly different perspective.

<div style="page-break-after: always;"></div>

###### Table 45-UEFI Shell commands for driver and device information

| **Command** | **Description**                                                                                        |
| ----------- | ------------------------------------------------------------------------------------------------------ |
| Drivers   | Lists all the UEFI drivers that follow the UEFI driver model. It uses the `GetDriverName()` service of the Component Name protocols to retrieve the human-readable name of each UEFI driver if it is available. It also shows the file path from which the UEFI driver was loaded. As UEFI drivers are loaded with the `Load` command, they appear in the list of drivers produced by the `Drivers` command. The `Drivers` command can also show the name of the UEFI driver in different languages. The code below shows the following examples of the `Drivers` command: <br/> Example 1: Shows the `Drivers` command being used to list the UEFI drivers in the default language. <br/> `fs0:> Drivers` <br/> Example 2: Shows the driver names in Spanish. <br/> `fs0:> Drivers -lsp` |
| Devices | Lists all the devices that are being managed or produced by UEFI drivers that follow the UEFI driver model. This command uses the `GetControllerName()` service of the Component Name protocols to retrieve the human-readable name of each device that is being managed or produced by UEFI drivers. If a human-readable name is not available, then the EFI device path is used. |
| DevTree | Similar to the `Devices` command. Lists all the devices being managed by UEFI drivers that follow the UEFI driver model. This command uses the `GetControllerName()` service of the Component Name Protocols to retrieve the human-readable name of each device that is being managed or produced by UEFI drivers. If the human-readable name is not available, then the EFI device path is used. This command also visually shows the parent/child relationships between all of the devices by displaying them in a tree structure. The lower a device is in the tree of devices, the more the device name is indented. The code below shows the following examples of the `DevTree` command: <br/><br/> Example 1: Displays the device tree with the device names in the default language. <br/> `fs0:> DevTree` <br/><br/> Example 2: Displays the device tree with the device names in Spanish. <br/> `fs0:> DevTree -lsp` <br/><br/> Example 3: Displays the device tree with the device names shown as EFI device paths. <br/> `fs0:> DevTree -d`  |
| Dh -d     | Provides a more detailed view of a single driver or a single device than the `Drivers`, `Devices`, and `DevTree` commands. If a driver binding handle is used with the `Dh -d` command, then a detailed description of that UEFI driver is provided along with the devices that the driver is managing and the child devices that the driver has produced. If a device handle is used with the `Dh -d` command, then a detailed description of that device is provided along with the drivers that are managing that device, that device's parent controllers, and the device's child controllers. If the `Dh -d` command is used without any parameters, then detailed information on all of the drivers and devices is displayed. The code below shows the following examples of the `Dh -d` command: <br/> Example 1: Displays the details on the UEFI driver on handle 27. <br/> `fs0:> Dh -d 27` <br/> Example 2: Displays the details for the device on handle 23. <br/> `fs0:> Dh -d 23` <br/> Example 3: Shows details on all the drivers and devices in the system. <br/> `fs0:> Dh -d` |
| OpenInfo  | Provides detailed information on a device handle managed by one or more UEFI drivers that follow the UEFI driver model. The `OpenInfo` command displays each protocol interface installed on the device handle, and the list of agents that have opened that protocol interface with the `OpenProtocol()` Boot Service. |

#### 31.3.6.1 Devices

This command lists all the devices that are being managed or produced by UEFI
drivers that follow the UEFI driver model. This command uses the
`GetControllerName()` service of the Component Name protocols to retrieve the
human-readable name of each device that is being managed or produced by UEFI
drivers. If a human-readable name is not available, then the EFI device path is
used.

* For Component Name: use the 3-letter language localization

* For Component Name2: use the 2x3-letter language localization

The code below shows the following examples of the `Devices` command. The -l
switch specifies the localized language.

* Example 1: Shows the `Devices` command being used to list the UEFI drivers in
the default language.

  `fs0:> Devices`

* Example 2: Shows the device names in Spanish.

  `fs0:> Devices -lspa fs0:> Devices -lsp`

This command is backwards compatible. If the system supports both the Component
Name Protocol and the Component Name2 Protocol, the driver can produce both
protocols. If the system supports only 2-letter localizations, an error is
generated if at attempt is made to enter the 2-leter localization.

#### 31.3.6.2 OpenInfo command

This command provides detailed information on a device handle that is being
managed by one or more UEFI drivers that follow the UEFI driver model. The
`OpenInfo` command displays each protocol interface installed on the device
handle, and the list of agents that have opened that protocol interface with
the `OpenProtocol()` Boot Service.

This command may be used to display information for devices or drivers.

* Example 1: The following example shows the `OpenInfo` command being used to
display the list of protocol interfaces on device handle 23 along with the list
of agents that have opened those protocol interfaces.

  `fs0:> OpenInfo 23`

* Example 2: The following example shows the `OpenInfo` command being used to
display the list of devices and/or child processes being managed by a driver.

  `fs0:> OpenInfo 15`

* Example 3: The `OpenInfo` command may be used along with the `Connect`,
`Disconnect`, and `Reconnect` commands to verify that a UEFI driver is opening
and closing protocol interfaces correctly. For example:

  `fs0:> Connect 23 fs0:> OpenInfo 23`
