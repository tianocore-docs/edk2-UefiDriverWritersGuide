<!--- @file
  12.4.4 Making configuration data available to other drivers

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

### 12.4.4 Making configuration data available to other drivers

Configuration data is stored in NVRAM. The data structures that contain the
static and configurable data for the driver are typically part of the package
published to the HII database. In order to make configuration data available to
other drivers, make sure to do the following:
1. Extract the forms from the form database.
2. Parse the forms for the names of the configurable options.
3. Use the HII Config Access Protocol to extract the data from all drivers.

#### 12.4.4.1 Check validity of configuration options for a specific device

The _UEFI Specification_ defines a `CallBack()` service in the HII Config
Access Protocol. This protocol interfaces with the VFR language. The callback
protocol includes an action, `QuestionId`, type, value, and action request.
When the user changes a configuration setting, this causes a call back to the
driver. The driver then needs to check to see if the value entered is valid.

The data structure for configuration options is initialized via the driver's
init entry point. The init reads the configuration data out of NVRAM and makes
sure the data is valid. If any particular variable is invalid, the value for
that variable is reset to its default.

The platform vendor can validate the configuration of all devices in the system
before booting. In addition, the devices can be reset to their default
configurations. If the firmware detects a corrupt configuration then a default
configuration may be selected automatically. The platform vendor may choose to
allow the user to select a menu item to force defaults on a specific device or
all devices at once.
