<!--- @file
  7.1 Optional Features

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

## 7.1 Optional Features

This section summarizes optional features impacting the implementation of the
driver entry point of a UEFI Driver. This is not a complete summary of all the
optional UEFI driver features.

_Table 21_ below provides the list of optional features and set of UEFI driver
categories.

If an entry in the table is empty, the feature does not ever apply to that
category of UEFI Driver and must never be implemented. If the entry in the
table contains a value such as 1.1 or 2.0, it means the feature optionally
applies to that category of UEFI Driver if, and only if, the UEFI Driver is
required to run correctly on platform firmware that conforms to that specific
version of the _EFI Specification_ or _UEFI Specification_.

For example, 2.0 refers to the _UEFI 2.0 Specification_, and 1.02 refers to the
_EFI 1.02 Specification_. If the entry in the table contains a value followed
by a '+' such as 1.1+ or 2.1+, then that means the feature optionally applies
to that category of UEFI Driver if the UEFI Driver is required to run correctly
on platform firmware that conforms to the version of the _EFI Specification_ or
_UEFI Specification_ specified by the value or higher values. For example, 2.0+ refers to the _UEFI 2.0, 2.1, 2.2, 2.3, and 2.3.1 Specifications_.

This table can be used to select features that apply to a specific UEFI Driver
implementation once the UEFI Driver writer knows what types of UEFI platforms
with which the UEFI Driver must be compatible as well as the set of optional
features the UEFI Driver must support.

For example, if a UEFI Driver is required to run on platforms that support UEFI
2.1 or higher, the Component Name Protocol, Driver Configuration Protocol,
Driver Configuration 2 Protocol, and Driver Diagnostics Protocol need not be
implemented because they apply only to UEFI platforms prior to UEFI 2.1.

The Driver Health Protocol may be optionally implemented, but the Driver Health
Protocol is expected to be used only by a platform that is UEFI 2.2 or higher.
In this case, the UEFI Driver must not depend on the Driver Health Protocol
being called to function correctly because it is not called by a UEFI 2.1
platform.

<div style="page-break-after: always;"></div>

###### Table 21-UEFI Driver Feature Selection Matrix

| <td colspan="4" style="font-weight:bold">UEFI Driver Model</td>  <td colspan="4" style="font-weight:bold">Non-UEFI Driver Model</td> |
| ----------------------------- | ------------------ | ------ | ----------------------- | ------ |  ------ |  ------ |  ------ |  ------ |
| Driver Binding Protocol       | 1.1+               | 1.1+   | 1.1+                    | 1.1+   |        |       |       |  |
| Component Name 2 Protocol     | 2.0+               | 2.0+   | 2.0+                    | 2.0+   |        |       |       |  |
| HII Packages                  | 2.1+               | 2.1+   | 2.1+                    | 2.1+   | 2.1+   | 2.1+  | 2.1+  |  |
| HII Config Access Protocol    |                    |        |                         |        | 2.1+   | 2.1+  | 2.1+  |  |
| Driver Health Protocol        | 2.2+               | 2.2+   | 2.2+                    | 2.2+   |        |       |       |  |
| Driver Diagnostics 2 Protocol | 2.0+               | 2.0+   | 2.0+                    | 2.0+   |        |       |       |  |
| Driver Family Override Protocol  | 2.1+               | 2.1+   | 2.1+                    | 2.1+   |        |       |       |  |
| Driver Supported EFI Version Protocol | 2.1+               | 2.1+   | 2.1+                    | 2.1+   | 2.1+   | 2.1+  | 2.1+  |  |
| Unload()                      | 1.02+              | 1.02 + | 1.02+                   | 1.02 + | 1.02 + | 1.02+ | 1.02+ |  |
| Exit Boot Services Event      | 1.02+              | 1.02 + | 1.02+                   | 1.02 + | 1.02 + | 1.02+ | 1.02+ |  |
| Set Virtual Address Map Event |                    |        |                         | 1.02 + | 1.02 + |       |       |  |
| Component Name Protocol       | 1.1                | 1.1    | 1.1                     | 1.1    |        |       |       |  |
| Driver Configuration Protocol | 1.1                | 1.1    | 1.1                     | 1.1    |        |       |       |  |
| Driver Configuration 2 Protocol  | 2.0                | 2.0    | 2.0                     | 2.0    |        |       |       |  |
| Driver Diagnostics Protocol   | 1.1                | 1.1    | 1.1                     | 1.1    |        |       |       |  |
