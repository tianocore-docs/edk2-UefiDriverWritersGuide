<!--- @file
  5.1.7 Stall()

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

### 5.1.7 Stall()

The `Stall()` service waits for a specified number of microseconds. In 32-bit
environments, the range of supported delays is from 1 μs to a little over an
hour. In 64-bit execution environments, the range of supported delays is from 1uS to
about 500,000 years. However, the delays passed into this service should be
short and are typically in the range of a few microseconds to a few
milliseconds.

**********
**Caution:** Implementations of the `Stall()` service may disable interrupts
and may block execution of other UEFI drivers. If long delays are required, use
a Timer Event instead. See `CreateEvent()`, `CreateEventEx()`, and `SetTimer()` for details._
**********

The `Stall()` service is very accurate and typically uses a high frequency
hardware timer or a calibrated software delay loop to implement the stall
functionality.

**********
**Caution:** `Stall()` may use a different timing source than the event
timer, and may have a higher or lower frequency and, hence, different accuracy.
**********

For hardware devices requiring delays between register accesses, use the
`Stall()` service. with a fixed stall value based in a hardware specification
for the device being accessed. The following example shows a use-case to
perform a fixed delay of 10 us between two PCI MMIO register writes.

###### Example 56-Fixed delay stall

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Protocol/PciIo.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT8 Value;

//
// Do a single 8-bit MMIO write to BAR #1, Offset 0x10 of 0xAA
//
Value = 0xAA;
Status = PciIo->Mem.Write (
                      PciIo,               // This
                      EfiPciIoWidthUint8,  // Width
                      1,                   // BarIndex
                      0x10,                // Offset
                      1,                   // Count
                      &Value               // Buffer
                      );
					  
//
// Wait 10 uS
//
gBS->Stall (10);
//
// Do a single 8-bit MMIO write to BAR #1, Offset 0x10 of 0x55
//
Value = 0x55;
Status = PciIo->Mem.Write (
                      PciIo,               // This
                      EfiPciIoWidthUint8,  // Width
                      1,                   // BarIndex
                      0x10,                // Offset
                      1,                   // Count
                      &Value               // Buffer
                      );
```

In this example, a UEFI drivers sends a command to a controller and then waits
for the command to complete. Use the `Stall()` service inside a loop to
periodically check for the completion status. The example below shows how to
poll for a completion status every millisecond and timeout after 100 ms.

###### Example 57-Poll for completion status using stalls

```c
#include <Uefi.h>
#include <Library/UefiBootServicesTableLib.h>
#include <Protocol/PciIo.h>

EFI_STATUS Status;
UINTN TimeOut;
EFI_PCI_IO_PROTOCOL *PciIo;
UINT8 Value;

//
// Loop waiting for the register at Offset 0 of Bar #0 of PciIo to
// become 0xE0 Wait 1 ms between each check of this register, and
// time out if it does not become 0xE0 after 100 mS.
//
for (TimeOut = 0; TimeOut <= 100000; TimeOut += 1000) {
  //
  // Do a single 8-bit MMIO read from BAR #0, Offset 0 into Value
  //
  Status = PciIo->Mem.Read (
                        PciIo,               // This
                        EfiPciIoWidthUint8,  // Width
                        0,                   // BarIndex
                        0,                   // Offset
                        1,                   // Count
                        &Value               // Buffer
                        );
  if (!EFI_ERROR (Status) && Value == 0xE0) {
    return EFI_SUCCESS;
  }
  //
  // Wait 1 ms
  //
  gBS->Stall (1000);
}
return EFI_TIMEOUT;
```
