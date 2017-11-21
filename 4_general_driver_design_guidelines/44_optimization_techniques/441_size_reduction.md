<!--- @file
  4.4.1 Size Reduction

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

### 4.4.1 Size Reduction

Table 15, below, lists techniques that can be used to reduce the size of UEFI
drivers. Significant size reductions can be realized by using combinations of
all of these techniques. The compiler and linker switches referenced below are
specific to the Microsoft* compilers. Different compilers and linkers may
use different switches for equivalent operations.

###### Table 15-Space optimizations

| Technique | Description |
|-----------|-------------|
| MdePkg and MdeModulePkg library classes | Maximizes the use of library classes defined in the MdePkg and MdeModulePkg. In some cases, multiple implementations of the same library class may be provided. Some implementations may be smaller and others may be faster. If a UEFI Driver implementation maximizes its use of library functions from EDK II packages, then the UEFI Driver can be built with library instance mappings defined in the DSC file that minimize the size of a UEFI Driver. |
| Compiler flags that optimize for size. | Some compiler provide flags (such as /Os and /O1) optimize code for space. This is an easy way to reduce the size of a UEFI driver. <br/>`Note:` _When optimization is turned on, each line of source code may not map to the same line when a debugger is active. This can make it more difficult to debug._ <br/>`TIP:` Be careful when turning on compiler optimizations because C source that works fine with optimizations disabled may stop working with optimizations enabled. They usually stop working because of missing `volatile` declarations on variables and data structures that are shared between normal contexts and raised TPL contexts or DMA bus masters. <br/>`TIP:` When optimized for speed, the UEFI driver is small, and may execute faster. If there are any speed paths in a UEFI driver that cause problems if the UEFI driver executes faster, then these switches may expose those speed paths. These same speed paths also show up as faster processors are used, so it is good to find these speed paths early. |
| Linker flags that remove unused functions and variables | Some linkers provide flags (such as /OPT:REF) that remove unused functions and variables from the executable image, including functions and variables in the UEFI driver and the libraries against which the UEFI driver is being linked. The combination of using the UEFI driver library with this linker switch can significantly reduce the size of a UEFI driver executable. The EDK II enabled these types of flags by default. |
| EFI Compression | If a UEFI Driver is stored in a PCI option ROM, the UEFI compression algorithm can be used to further reduce the size of a UEFI driver. The build utility `EfiRom` has built-in support for compressing UEFI images. The PCI bus driver has built-in support for decompressing UEFI drivers stored in PCI option ROMs. The average compression ratio on IA32 is 2.3, and the average compression ratio on the IPF platform is 2.8 The EfiRom utility is described in _Chapter 18_ of this guide.  |
| EFI Byte Code Images | If a UEFI driver is going to be stored in a PCI option ROM, and the PCI option ROM must support both IA32 and IPF platforms, or just IPF platforms, EFI Byte Code (EBC) executables should be considered. EBC executables are portable between IA32 and IPF platforms. This portability means that only a single UEFI driver image is required to support both IA32 and IPF platforms. Also, the EBC executables are significantly smaller than images for the IPF platform, so there are advantages to using this format for UEFI drivers that are targeted only at IPF platforms. In addition, using EFI Compression (see above) can reduce the EBC executables even further. |
