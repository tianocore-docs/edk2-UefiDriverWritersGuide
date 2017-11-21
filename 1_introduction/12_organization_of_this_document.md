<!--- @file
  1.2 Organization of this document

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

## 1.2 Organization of this document

This document is not intended to be read front to back. Use it more as a
cookbook for developing and implementing drivers. The following table describes
the organization of this document.

<div style="page-break-after: always;"></div>

###### Table 1-Organization of the UEFI Driver Writers Guide

| **Chapter** | **Description**                                                 |
| ----------- | --------------------------------------------------------------- |
| 1. Introduction   | Introduction and list of references related to UEFI Driver development.   |
| 2. Checklist      | Checklist, or basic recipe, for UEFI Driver development.     |
| 3. Foundation     | Foundation and terms related to UEFI Driver development.    |
| 4-17. Common Features  | Recommendations for features common to most UEFI Driver types. Many of these features are optional and inclusion of them depends on the requirements for a specific UEFI Driver.       |
| 18-21. Industry Standard Busses   | Recommendations for UEFI Drivers that manage controllers on Industry standard buses such as PCI, USB, SCSI and SATA.   |
| 22-27. Console and OS Boot Devices | Recommendations for UEFI Drivers that produce protocols that directly or indirectly provide services for a UEFI Boot Manager to initialize consoles and boot a UEFI conformant operating system from a boot device. This includes text consoles, serial ports, graphical consoles, mass storage devices, network devices and boot devices not defined by the _UEFI Specification_. |
| 28-29. CPU Specific   | Special considerations for IPF and EBC platforms.     |
| 30-32. Build/Release   | Best practices for building, testing, debugging and distributing UEFI Drivers.  |
    | Appendix A. File Templates              | Source file templates for UEFI Drivers, Protocols, GUIDs, and Library Classes
| Appendix B. EDK II Drivers   | Table of UEFI Driver features found in EDK II driver implementations.  |
| Appendix C. Glossary         | Glossary of terms used in this guide.    |
