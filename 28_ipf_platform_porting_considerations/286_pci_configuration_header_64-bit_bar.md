<!--- @file
  28.6 PCI Configuration Header 64-bit BAR

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

## 28.6 PCI Configuration Header 64-bit BAR

Another source of alignment faults is when 64-bit BAR values are accessed in a
PCI configuration header. A PCI configuration header has room for up to six
32-bit BAR values or three 64-bit BAR values. A PCI configuration header may
also contain a mix of both 32-bit BAR values and 64-bit BAR values. All 32-bit
BAR values are guaranteed to be on a 32-bit boundary. However, 64-bit BAR
values may be on a 32-bit boundary or a 64-bit boundary. As a result, every
time a 64-bit BAR value is accessed, it must be assumed to be on a 32-bit
boundary in order to guarantee that an alignment fault is not generated.

The following two methods can be used to prevent an alignment fault when a
64-bit BAR value is extracted from a PCI configuration header:

* Use `ReadUnaligned64()` to read the BAR contents

* Use `CopyMem()` to transfer the BAR contents into a 64-bit aligned location.

* Collect the two 32-bit values that compose the 64-bit BAR, and combine them
  into a 64-bit value.

The example below shows the incorrect method of extracting a 64-bit BAR from a
PCI configuration header, and then shows three correct methods.

###### Example 249-Accessing a 64-bit BAR in a PCI configuration header

```c
#include <Uefi.h>
#include <IndustryStandard/Pci.h>
#include <Library/BaseMemoryLib.h>
#include <Library/BaseLib.h>

UINT64
EFIAPI
Get64BitBarValue (
   PCI_TYPE00  *PciConfigurationHeader,
   UINTN       BarOffset
  )
  
{
  UINT64  *BarPointer64;
  UINT32  *BarPointer32;
  UINT64  BarValue;
  
  BarPointer64 = (UINT64 *)((UINT8 *)PciConfigurationHeader + BarOffset);
  BarPointer32 = (UINT32 *)((UINT8 *)PciConfigurationHeader + BarOffset);
  
  //
  // Wrong. May cause an alignment fault.
  //
  BarValue = *BarPointer64;
  
  //
  // Correct. Guaranteed not to generate an alignment fault.
  //
  BarValue = ReadUnaligned64 (BarPointer64);
  
  //
  // Correct. Guaranteed not to generate an alignment fault.
  //
  CopyMem (&BarValue, BarPointer64, sizeof (UINT64));
  
  //
  // Correct. Guaranteed not to generate an alignment fault.
  //
  BarValue = (UINT64)(*BarPointer32 | LShiftU64 (*(BarPointer32 + 1), 32));
  
  return BarValue;
}
```
