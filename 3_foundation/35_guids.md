<!--- @file
  3.5 GUIDs

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

## 3.5 GUIDs

A UEFI programming environment provides software services through the UEFI Boot
Services Table, the UEFI Runtime Services Table, and Protocols installed into
the handle database. Protocols are the primary extension mechanism provided by
the _UEFI Specification_. Protocols are named using a GUID.

A GUID is a unique 128-bit number that is a globally unique identifier (a
universally unique identifier, or UUID). Each time an image, protocol, device,
or other item is defined in UEFI, a GUID must be generated for that item. The
example below shows the structure definition for an `EFI_GUID` in the EDK II
along with the definition of the GUID value for the EFI Driver Binding Protocol
from the _UEFI Specification_.

###### Example 1-EFI_GUID data structure in EDK II

```c
///
/// 128 bit buffer containing a unique identifier value.
/// Unless otherwise specified, aligned on a 64 bit boundary.
///
typedef struct {
  UINT32  Data1;
  UINT16  Data2;
  UINT16  Data3;
  UINT8   Data4[8];
} GUID;

///
/// 128-bit buffer containing a unique identifier value.
///
typedef GUID EFI_GUID;

///
/// The global ID for the Driver Binding Protocol.
///
#define EFI_DRIVER_BINDING_PROTOCOL_GUID \
  { \
    0x18a031ab, 0xb443, 0x4d1a, {0xa5, 0xc0, 0xc, 0x9, 0x26, 0x1e, 0x9f, 0x71 } \ 
  }
```

**********
**TIP:** New GUID values can be generated using the **GUIDGEN** utility shipped
with Microsoft* compilers, or the **uuidgen** command under Linux. Other
GUID generation utilities may be found using internet searches.
**********

Protocol services are registered in the handle database using the GUID name of
the Protocol and Protocol services are discovered by looking up Protocols in
the handle database using the GUID name associated with the Protocol to perform
the lookup operation.

UEFI fundamentally assumes that a specific GUID exposes a specific protocol
interface (or other item). Because a protocol is "named" by a GUID (a unique
identifier), there should be no other protocols with that same GUID. Be careful
when creating protocols to define a new, unique GUID for a new protocol.

Put another way, the GUID forms a contract: If the UEFI Driver finds a protocol
with a particular GUID, it may assume that the contents of the protocol are as
specified for that protocol. If the contents of the protocol are different, the
driver that published the protocol is assumed to be in error.

In some ways, GUIDs are can be viewed as contracts. If a UEFI Driver looks up a
protocol with a certain GUID, the structure under the GUID is well defined. If
the GUID is duplicated, this 1:1 mapping breaks. If a GUID is copied and
applied to a new protocol, the users of the old protocol call the new protocol
expecting the old interfaces or vice versa. Either way, the results are never
good.

**********
**Caution:** There are improper practices to create new GUID values. For
example, cutting and pasting an existing GUID, hand-modifying an existing GUID,
or
**********

_incrementing/decrementing fields in a GUID creates the opportunity to
introduce a duplicate GUID. These practices can cause `catastrophic failures.`
Typically, a system containing a duplicate GUID may inadvertently find the new
protocol and think that it is another protocol, which mostly likely crashes the
system. Another possible failure is a data-loss failure caused when a
duplicated GUID is a data-handling GUID (such as a disk I/O, file-system or
NVRAM-handling GUID). Always use a GUID generator utility to create new GUIDs._

**********
**TIP:** Bugs caused by duplicate GUIDs are typically very difficult to root
cause and many developers do not check the GUID when debugging. If the root
cause for a hang has not been found in a reasonable amount of time, check to
make sure the GUID for each relevant protocol is unique.
**********
