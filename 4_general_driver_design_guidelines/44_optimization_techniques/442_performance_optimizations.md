<!--- @file
  4.4.2 Performance Optimizations

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

### 4.4.2 Performance Optimizations

The following table lists the techniques to use to improve the performance of
UEFI drivers. By using combinations of all of these techniques, significant
performance enhancements can be realized.

###### Table 16-Speed optimizations

| **Technique**                                | **Description** |
| --------------------------------------------- | --------------- |
| Compiler flags that optimize for performance. | Some compiler provide flags (such as /Ot, /O2, and /Ox) optimize code for performance. This technique is an easy way to reduce the execution time of a UEFI driver. For the most part, the EDK II balances size and speed optimizations. The flags can be customized to specify a preference for speed or size. <br/>`Note:` _When optimization is turned on, each line of source code may not map to the same line when a debugger is active. This can make it more difficult to debug._ <br/>`TIP:` Be careful when turning on compiler optimizations because C source that works fine with optimizations disabled may stop working with optimizations enabled. They usually stop working because of missing `volatile` declarations on variables and data structures that are shared between normal contexts and raised TPL contexts. <br/>`TIP:` Because the UEFI driver is small, it may execute faster. If there are any speed paths in a UEFI driver that cause problems if the UEFI driver executes faster, then these switches may expose those speed paths. These same speed paths also show up as faster processors are used, so it is good to find these speed paths early. |
| UEFI Services | Whenever possible, use UEFI boot services, UEFI runtime services, and the protocol services provided by other UEFI drivers. The UEFI boot services and UEFI runtime services are likely implemented as native calls that have been optimized for the platform, so there is a performance advantage for using these services. Some protocol services might be native, and other protocol services might be EBC images. Either way, if all UEFI drivers assume that external protocol services are native, then the combination of UEFI drivers and EFI services result in more efficient execution. |
| PCI I/O Protocol | If a UEFI driver is a PCI driver, then it should take advantage of all the PCI I/O Protocol services to improve the UEFI driver's performance. This approach means that all register accesses should be performed at the largest possible size. For example, perform a single 32-bit read instead of multiple 8-bit reads. Also, take advantage of the read/write multiple, FIFO, and fill modes of the `Io()`, `Mem()`, and `Pci()` services. See _Chapter 18_ for details on optimization techniques that are specific to PCI. |
