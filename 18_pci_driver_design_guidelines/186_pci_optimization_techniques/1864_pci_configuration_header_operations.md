<!--- @file
  18.6.4 PCI Configuration Header Operations

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

### 18.6.4 PCI Configuration Header Operations

The following _three examples_ demonstrate different methods to read a PCI
configuration header from a PCI controller, ordered lowest to highest in
performance. The first example uses a loop to read the header 8 bits at a time;
the second uses a single call to read the entire header 8 bits at a time and
the third uses a single call to read the header 32 bits at a time.

###### Example 190-Read PCI configuration using a loop

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>
#include <IndustryStandard/Pci.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
PCI_TYPE00 Pci;
UINT32 Index;

//
// Loop reading the 64-byte PCI configuration header 8 bits at a time
//
for (Index = 0; Index < sizeof (Pci); Index++) {
  Status = PciIo->Pci.Read (
                        PciIo,                            // This
                        EfiPciIoWidthUint8,               // Width
                        Index,                            // Offset
                        1,                                // Count
                        (UINT8 *)(&Pci) + Index           // Buffer
                        );
}
```

###### Example 191-Read PCI configuration 32 bits at a time

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>
#include <IndustryStandard/Pci.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
PCI_TYPE00 Pci;

//
// This is a faster method that removes the loop and reads 8 bits at a time.
//
Status = PciIo->Pci.Read (
                      PciIo,                              // This
                      EfiPciIoWidthUint8,                 // Width
                      0,                                  // Offset 
                      sizeof (Pci),                       // Count 
                      &Pci                                // Buffer
                      );
```

###### Example 192-Read PCI configuration 32 bits at a time

```c
#include <Uefi.h>
#include <Protocol/PciIo.h>
#include <IndustryStandard/Pci.h>

EFI_STATUS Status;
EFI_PCI_IO_PROTOCOL *PciIo;
PCI_TYPE00 Pci;

//
// This is the fastest method that makes a single call to PCI I/O and reads the
// PCI configuration header 32 bits at a time.
//
Status = PciIo->Pci.Read (
                      PciIo,                              // This
                      EfiPciIoWidthUint32,                // Width
                      0,                                  // Offset 
                      sizeof (Pci) / sizeof (UINT32),     // Count 
                      &Pci                                // Buffer
                      );
```
