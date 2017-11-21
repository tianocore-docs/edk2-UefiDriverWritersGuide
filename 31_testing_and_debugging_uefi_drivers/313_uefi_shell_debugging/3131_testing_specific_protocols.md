<!--- @file
  31.3.1 Testing Specific Protocols

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

### 31.3.1 Testing Specific Protocols

The following table lists Shell commands that might be useful in testing
specific protocols.

<div style="page-break-after: always;"></div>

###### Table 40-UEFI Shell commands

| **Command** | **Protocol tested**                             | **Service tested**                   |
| ----------- | ----------------------------------------------- | ------------------------------------ |
| Load -nc   |                                                 | Driver entry point.                                                                                                                                              |
|            | Driver Binding                                  | Supported()                                                                                                                                                      |
| Load       |                                                 | Driver entry point.                                                                                                                                              |
|            | Driver Binding                                  | Supported()                                                                                                                                                      |
|            | Driver Binding                                  | Start()                                                                                                                                                          |
| Unload     | Loaded Image                                    | Unload()                                                                                                                                                         |
| Connect    | Driver Binding                                  | Supported()                                                                                                                                                      |
|            | Driver Binding                                  | Start()                                                                                                                                                          |
| Disconnect | Driver Binding                                  | Stop(). Note: The UEFI driver must specifically disconnect (destroy) all child handles and device paths associated with the handle for the driver being stopped. |
| Reconnect  | Driver Binding                                  | Supported()                                                                                                                                                      |
|            | Driver Binding                                  | Start()                                                                                                                                                          |
|            | Driver Binding                                  | Stop()                                                                                                                                                           |
| Drivers    | Component Name and Component Name2              | GetDriverName()                                                                                                                                                  |
| Devices    | Component Name and Component Name2              | GetDriverName()                                                                                                                                                  |
|            |                                                 | GetControllerName()                                                                                                                                              |
| DevTree    | Component Name and Component Name2              | GetControllerName()                                                                                                                                              |
| Dh -d      | Component Name and Component Name2              | GetDriverName()                                                                                                                                                  |
|            |                                                 | GetControllerName()                                                                                                                                              |
| DrvCfg -s  | Driver Configuration and Driver Configuration 2 | This command used to test the SetOptions() service. These protocols are supported by EFI 1.10 and UEFI 2.0.                                                      |
| DrvCfg -f  | Driver Configuration and Driver Configuration 2 | This command used to test the                                                                                                                                    |
|            |                                                 | ForceDefaults() service. These protocols are supported by EFI 1.10 and UEFI 2.0.                                                                                 |
| DrvCfg -v  | Driver Configuration and Driver Configuration 2 | This command used to test the OptionsValid() service. These protocols are supported by EFI 1.10 and UEFI 2.0.                                                    |
| DrvDiag    | Driver Diagnostics and Driver Diagnostics2      | RunDiagnostics()                                                                                                                                                 |
