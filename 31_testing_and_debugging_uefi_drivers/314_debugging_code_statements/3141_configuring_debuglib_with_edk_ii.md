<!--- @file
  31.4.1 Configuring DebugLib with EDK II

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

### 31.4.1 Configuring DebugLib with EDK II

The EDK II provides several methods to manage the DebugLib macros. These
include:

* `MDEPKG_NDEBUG` macro

* DebugLib library instances

* DebugLib Platform Configuration Database (PCD) settings

#### 31.4.1.1 MDEPKG_NDEBUG Define

If `MDEPKG_NDEBUG` is defined when a UEFI Driver is built, then all the
`DebugLib` macros used by a UEFI Driver are removed. This provides a smaller
executable, but all debug log messages, assert condition checks, and debug code
are removed from the UEFI Driver that is produced by the EDK II build. The
example below shows the addition of a `[BuildOptions]` section to the DSC files
from _Chapter 30_. It forces `MDEPKG_NDEBUG` to be defined for `RELEASE`
builds, which means all the `DebugLib` macros are disabled when the `-b RELEASE` flag is used when building a UEFI Driver.

###### Example 266-EDK II Package DSC File with Build Options

```ini
[BuildOptions]
  GCC:RELEASE_*_*_CC_FLAGS   = -DMDEPKG_NDEBUG
  INTEL:RELEASE_*_*_CC_FLAGS = /D MDEPKG_NDEBUG
  MSFT:RELEASE_*_*_CC_FLAGS  = /D MDEPKG_NDEBUG
```

#### 31.4.1.2 DebugLib Library Instances

The `MdePkg` provides 4 different implementations of the `DebugLib` library
class. These are:

* `MdePkg/Library/BaseDebugLibNull/BaseDebugLibNull.inf`

* `MdePkg/Library/BaseDebugLibConOut/BaseDebugLibConOut.inf`

* `MdePkg/Library/BaseDebugLibStdErr/BaseDebugLibStdErr.inf`

* `MdePkg/Library/BaseDebugLibSerialPort/BaseDebugLibSerialPort.inf`
  `BaseDebugLibNull` is an implementation of the `DebugLib` with empty worker
  functions. This means the `DebugLib` macros are mapped to empty worker
  functions, so if the library instances is used by a UEFI Driver, no debug log
  messages, assert condition checks, or debug code are active. Using this
  library mapping is not as small as using `MDEPKG_NDEBUG`, but switching to
  this library mapping does not require a rebuild of the UEFI Driver sources.

`BaseDebugLibStdErr` is the recommended library instance for UEFI drivers that
are being debugged and is the library that is used in the example DSC file in
_Chapter 30_. This sends all messages to the Standard Error console in the UEFI
System Table. If there is no output, then the likely cause is that the Standard
Error device is not configured. Use the platform setup to configure the
Standard Error.

`BaseDebugLibConOut` may be used as a substitute for `BaseDebugLibStdErr` when
it is not possible to get the Standard Error console configured. This sends all
messages to the Standard Output console in the UEFI System Table. This mixes
debug messages with the normal console activity, so the display may be
difficult to read, and since most UEFI consoles do not support scroll up
operations, it may be difficult to see the messages when many are displayed.

`BaseDebugLibSerialPort` is not a UEFI conformant DebugLib. It directly
accesses serial port hardware through a `SerialPortLib` library instance. This
can be useful when debugging UEFI Drivers that execute before UEFI consoles are
initialized, such as UEFI Drivers that are loaded and executed from a PCI
Option ROM. When this library instance is used, the UEFI Driver writer must
know that there is a serial port available on the target platform under test
and must configure a `SerialPortLib` with for the attributes of the specific
serial port that is to be used.

#### 31.4.1.3 DebugLib Platform Configuration Database Settings

The `MdePkg` library class `DebugLib` uses several Platform Configuration
Database (PCD) setting to control the behavior of the DebugLib macros. The
token names for these PCD settings are as follows:

* `gEfiMdePkgTokenSpaceGuid.PcdDebugPropertyMask`

* `gEfiMdePkgTokenSpaceGuid.PcdDebugPrintErrorLevel`

* `gEfiMdePkgTokenSpaceGuid.PcdDebugClearMemoryValue`

`PcdDebugPropertyMask` provides fine grain control over the macros provided by
the `DebugLib`. The previous two sections discuss how to disable the entire
`DebugLib` and how to select different `DebugLib` library instances.
`PcdDebugPropertryMask` is a bit mask that allows individual `DebugLib` macro
types to be enabled or disabled. The example below shows the bitmask
definitions. `0x01` enables `ASSERT()` macros. `0x02` enables `DEBUG()` macros.
`0x04` enables the 3 `DEBUG_CODE()` macros. `0x08` enables the

`DEBUG_CLEAR_MEMORY()` macro. `0x10` and `0x20` control the behavior of the
`ASSERT()` macro if the assert condition evaluates to `FALSE`. `0x10` causes a
CPU breakpoint to be generated, which is useful if a source level debugger is
being used, and `0x20` causes the CPU to enter an infinite loop so execution of
the UEFI Driver stops.

```c
//
// Declare bits for PcdDebugPropertyMask
//
#define DEBUG_PROPERTY_DEBUG_ASSERT_ENABLED 0x01
#define DEBUG_PROPERTY_DEBUG_PRINT_ENABLED 0x02
#define DEBUG_PROPERTY_DEBUG_CODE_ENABLED 0x04
#define DEBUG_PROPERTY_CLEAR_MEMORY_ENABLED 0x08
#define DEBUG_PROPERTY_ASSERT_BREAKPOINT_ENABLED 0x10
#define DEBUG_PROPERTY_ASSERT_DEADLOOP_ENABLED 0x20
```

`Example 267-PcdDebugPropertyMask bitmask PcdDebugPrintErrorLevel` provides a
bitmask of the debug error levels that are currently enabled. The debug print
error levels are shown in the Error Levels table above. Any combination of the
values can be set in the bitmask. If a bit is set, then `DEBUG()` macros with
that same `ErrorLevel` bit set are printed.

`PcdDebugClearMemoryValue` provides the 8-bit byte value to use when

`DEBUG_CLEAR_MEMORY()` macros are used. This value is typically set to `0x00`,
but it is usually a good idea to try a few different values to make sure code
is not improperly using buffer contents that have been cleared.

The following example shows the addition of a `[PcdsFixedAtBuild]` section to
the DSC files from _Chapter 30_. It sets `PcdDebugPropertyMask` so `DEBUG()`,
`ASSERT()`, and `DEBUG_CODE()` macros are enabled and a breakpoint is generated
when an `ASSERT()` is triggered. It also sets the `PcdDebugPrintErrorLevel` at
a fairly high verbosity level with `DEBUG_ERROR`, `DEBUG_INFO`, `DEBUG_LOAD`,
`DEBUG_WARN`, and `DEBUG_INIT` all enabled. Finally, it configures
`PcdDebugClearMemoryValue` so `DEBUG_CLEAR_MEMORY()` macros, when they are
enabled, fill buffers with `0x00`.

###### Example 268-EDK II Package DSC File with Build Options

```ini
[PcdsFixedAtBuild]
  gEfiMdePkgTokenSpaceGuid.PcdDebugPropertyMask|0x17
  gEfiMdePkgTokenSpaceGuid.PcdDebugPrintErrorLevel|0x80000047
  gEfiMdePkgTokenSpaceGuid.PcdDebugClearMemoryValue|0x00
```
