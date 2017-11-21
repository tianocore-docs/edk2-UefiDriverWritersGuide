<!--- @file
  4.2.13 Do not use hidden PCI Option ROM Regions

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

### 4.2.13 Do not use hidden PCI Option ROM Regions

Some option ROMs may use paging or other techniques to load and execute code
that was not visible to the system firmware when measuring the visible portion
of the option ROM. This technique is discouraged because it is the PCI bus
driver's responsibility to extract the option ROM contents when a PCI bus
enumerates. If code were required to access hidden portions of an option ROM,
then the PCI bus driver would not have the ability to extract the additional
PCI Option ROM contents.

This inability means that the UEFI drivers in a PCI Option ROM must be visible
without accessing a hidden portion of a PCI Option ROM. However, if there is a
safe mechanism to access the hidden portions of the PCI option ROM after the
UEFI drivers have been loaded and executed, then the UEFI driver may choose to
access those contents. For example, non-volatile configuration information,
utilities, or diagnostics can be stored in the hidden PCI Option ROM regions.

**********
**Caution:** The hidden option ROM regions are also not measurable via UEFI 2.3
and beyond signing and verification interfaces. This makes them, and the
system, less secure.
**********
