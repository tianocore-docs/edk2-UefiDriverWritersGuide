<!--- @file
  28 IPF Platform Porting Considerations

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

# 28 IPF Platform Porting Considerations

When writing a UEFI driver, there are steps that can be taken to help make sure
the driver functions properly on an IPF platform. The guidelines listed in this
chapter help improve the portability of UEFI drivers, and explain some of the
pitfalls that may be encountered when a UEFI driver is ported to an IPF
platform.

_Chapter 4_ covers the general guidelines for implementing a UEFI Driver that
is compatible with both 32-bit and 64-bit CPU architectures. If a 32-bit UEFI
Driver is being ported to IPF, then make sure the guidelines from _Chapter 4_
are followed. This chapter focuses on issues that are specific to IPF. In
general, the guidelines for implementing a UEFI Driver for IPF are more
rigorous that other CPU architectures. If a UEFI Driver is implemented and
validated for IPF, then there is a good chance that the UEFI Driver can be
easily ported to most of the other CPU architecture supported by the _UEFI
Specification_.

In addition, the _DIG64 Specification_ requires some protocols that are
considered obsolete by the latest _UEFI Specification_. This means UEFI Drivers
for IPF may have to produce some extra protocols from older versions of the
_EFI Specification_ and _UEFI Specification_ in order to be conformant with the
_DIG64 Specification_. The additional protocols are listed below. Other
chapters of the guide provide recommendations on how to implement these
protocols and this topic will not be covered further in this chapter.

* Component Name Protocol

* Driver Configuration Protocol

* Driver Diagnostics Protocol
