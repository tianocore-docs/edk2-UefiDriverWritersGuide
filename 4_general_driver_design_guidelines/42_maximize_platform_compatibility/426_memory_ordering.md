<!--- @file
  4.2.6 Memory ordering

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

### 4.2.6 Memory ordering

Not all processors have strongly ordered memory models and some compilers, when
high levels of optimization are enabled, may induce memory ordering issues.
Weak ordering means that the order in which memory transactions are presented
in the C source code may not be the same order of operations when the code is
executed. IPF platforms are weakly ordered, so UEFI Drivers that are compiled
for IPF have to be aware of this issue. See _A Formal Specification of Intel
Itanium Processor Family Memory Ordering_ for a detailed discussion of this
topic. It is also discussed in the _Intel Itanium Architecture Software
Developer Manuals._

**********
**TIP:** Most of the details of memory ordering are taken care of by protocols
and libraries. If protocols and libraries are used to access hardware, then
memory ordering issues should be hidden from a UEFI Driver implementation. A
direct access to hardware is **not recommended**
**********

Normally, memory ordering is not an issue, because the processor and the
compiler guarantee that the code executes as the developer expects. However,
UEFI drivers that access DMA buffers that are simultaneously accessed by both
the processor and the DMA bus master may run into issues if either the
processor or the DMA bus master, or both, are weakly ordered. The DMA bus
master must resolve its own memory ordering issues, but a UEFI Driver is
responsible for managing the processor's ordering issues.

The classic case where strong ordering versus weak ordering produces different
results is when there is a memory-based FIFO and a shared bus master "doorbell"
register that is shared by all additions to the FIFO. In this common
implementation, the driver (producer) formats a new request descriptor and, as
its last logical operation, writes the value indicating the entry is valid.

This mechanism becomes a problem if a new request is being added to the FIFO
while the bus master is checking the next FIFO entry's valid flag. It is
possible for the "last write" issued by the processor (that turns on the valid
flag) to be posted to memory before the logically earlier writes that finish
initializing the FIFO/request descriptor. The solution in this case is to ensure that all pending memory writes have been completed before the "valid flag" is enabled. There are two techniques to avoid this problem:

* `Technique 1:` Declare C data structures or portions of C data structures
  with the `volatile` attribute. The compiler ensures that strong ordering is
  used for all operations to with that declaration.

* `Technique 2:` Use the EDK II library `BaseLib` function called
  `MemoryFence()`. This function guarantees that all the transactions in the
  source code prior to the `MemoryFence()` function are completed before the
  code after the

`MemoryFence()` function is executed. On IPF platforms, this function executes
a memory fence instruction. Some compilers provide an intrinsic function that
declares a barrier and, if this intrinsic is provided, the EDK II
implementation of `MemoryFence()` includes that barrier intrinsic. The barrier
intrinsic is not really a call. Instead, it prevents memory read/write
transactions from being moved across the barrier as part of the compiler code
generation. This may be very important when high levels of compiler
optimization are enabled.

The second technique is preferred for readability because the intent is
clearer. A `volatile` declaration tends to hide what was needed, because it is
not part of the affected code (it is off in a structure definition). In
addition, the `volatile` declaration may impact the performance of the UEFI
Driver's because all memory transactions to the structure are strongly ordered.

It is recommended that these techniques be used appropriately in all driver
types to maximize the UEFI driver's platform compatibility.
