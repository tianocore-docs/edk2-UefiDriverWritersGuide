<!--- @file
  4.3 Maximize CPU Compatibility

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

## 4.3 Maximize CPU Compatibility

UEFI Drivers should be designed to maximize source code portability since it is
possible to write a single UEFI Driver that compiles on all CPU architectures
supported by the _UEFI Specification_. The list of supported CPU architectures
may grow over time, so it is important to follow these portability guidelines.

The guidelines presented here apply to all CPU architectures. _Chapter 28_
covers portability issues specific to IPF platforms, and _Chapter 29_ covers
portability issues that are specific to EBC.

When porting between CPU architectures, most developers take as much existing
code as possible and reuse it. Unfortunately, some developers porting code do
not rigorously follow the UEFI conventions, such as using only the data types
defined in the Calling Conventions section of the _UEFI Specification_. Others
may not follow best coding practices.

* Use data types defined by the Calling Conventions section of the _UEFI
  Specification_.

* Use compiler flag settings to guarantee that the UEFI calling conventions for
  the CPU architecture are followed. See the Calling Conventions section of the
  _UEFI Specification_ for details.

* If a UEFI driver contains assembly language sources, then either the source
  needs to be ported or it needs to be converted to C language source.
  Conversion to C language source is `recommended.` The EDK II library
  `BaseLib,` and other EDK II libraries, provide functions that may reduce, or
  even eliminate, the need to assembly code in UEFI Drivers.

**********
**TIP:** Implement UEFI Drivers in C to maximize portability,
**********

* Avoid use of C++. It is not supported by EBC.

* Avoid unaligned data accesses. Compilers, by default, generate code and data
  that perform aligned accesses. Unaligned data accessed are generated when
  features such as byte-packed structures, type casting pointers, or assembly
  language are used. Aligned data accesses typically execute faster than
  unaligned data accesses. Parsing UEFI Device Paths is a common generator of
  unaligned data accesses. These generate alignment faults on IPF platforms.

* The best approach to debugging a UEFI Driver ported to a differing CPU
  architecture is to keep a good code base with every revision. This allows
  comparison with earlier revisions to see the source code before and after the
  problem became visible.

* If source code is not available, the CPU register state may not be sufficient
  to debug a specific issue. Keep in mind that a "new" problem might have
  nothing to do with a recent change to the code. A pre-existing problem might
  not have shown up before for a variety of reasons. For example, the current
  developer might have included error checking or exercised the error handling
  registers after making an addition to the code-error checking that might not
  have been done before. Or a new addition might make the pre-existing problem
  worse, so the problem finally becomes visible in the new revision.

* Perform a minimal port first to test simple parts of the UEFI driver. This is
  simply good porting practices, but even experienced developers can forget to
  port and test the simple things first. Start with a known-good sample driver
  that is extremely simple. For example, a driver that prints "Hello World".
  Then divide the code into sections. Begin inserting and testing the less
  complicated sections into the known-good driver, one section at a time.
  Another technique is to replace more complex code with "neutered" code that
  returns but doesn't actually do anything. Make sure the simple sections work
  and do not cause alignment faults or other errors. Only then should the more
  complicated sections be added and adapted to the new architecture rules. This
  approach can significantly cut down on debug time.
