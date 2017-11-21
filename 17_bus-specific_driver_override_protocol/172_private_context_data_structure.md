<!--- @file
  17.2 Private Context Data Structure

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

## 17.2 Private Context Data Structure

The following example shows a fragment of a private context data structure used
to manage the child-device-related information in a bus driver producing the
Bus Specific Driver Override Protocol. The _BusSpecificDriverOverride_ field is
the protocol instance for the Bus Specific Driver Override Protocol. The
_NumberOfHandles_ field is the number of image handles that the `GetDriver()`
function of the Bus Specific Driver Override Protocol returns for a single
child device. The _HandleBufferSize_ field is the number of handles allocated
for the array _HandleBuffer_, and the _HandleBuffer_ field is the array of
driver image handles returned by the `GetDriver()` function of the Bus Specific
Driver Override Protocol. The `CR()` macro provides a method to retrieve a pointer to
an `ABC_PRIVATE_DATA` instance from a Bus Specific Driver Override Protocol _This_
pointer. This macro is used by the `GetDriver()` function to retrieve the
private context structure.

###### Example 163-Private Context Data Structure with a Bus Specific Driver Override Protocol

```c
#define ABC_PRIVATE_DATA_SIGNATURE SIGNATURE_32('A','B','C',' ')

typedef struct {
  UINTN Signature;
  EFI_BUS_SPECIFIC_DRIVER_OVERRIDE_PROTOCOL BusSpecificDriverOverride;
  UINTN NumberOfHandles;
  UINTN HandleBufferSize;
  EFI_HANDLE *HandleBuffer;
} ABC_PRIVATE_DATA;

#define ABC_PRIVATE_DATA_FROM_BUS_SPECIFIC_DRIVER_OVERRIDE_THIS(a) \
  CR(a, ABC_PRIVATE_DATA, BusSpecificDriverOverride, ABC_PRIVATE_DATA_SIGNATURE)
```

This example shows how the private context data structure must be initialized
by the bus driver when a child controller is discovered. This initialization is
required for the examples of the `AbcGetDriver()` and `AbcAddDriver()`
functions shown below to work correctly.

###### Example 164-Private Context Data Structure Initialization

```c
Private->Signature = ABC_PRIVATE_DATA_SIGNATURE;
Private->BusSpecificDriverOverride.GetDriver = AbcGetDriver;
Private->NumberOfHandles = 0;
Private->HandleBufferSize = 0;
Private->HandleBuffer = NULL;
```
