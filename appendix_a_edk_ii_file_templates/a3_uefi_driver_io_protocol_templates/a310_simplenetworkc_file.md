<!--- @file
  A.3.10 _SimpleNetwork.c File_

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

### A.3.10 _SimpleNetwork.c File_

###### Example A-22-Simple Network Protocol implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include "<<DriverName>>.h"

///
/// Simple Network Protocol instance
///
GLOBAL_REMOVE_IF_UNREFERENCED
EFI_SIMPLE_NETWORK_PROTOCOL g<<DriverName>>SimpleNetwork = {
  EFI_SIMPLE_NETWORK_PROTOCOL_REVISION,       // Revision
  <<DriverName>>SimpleNetworkStart,           // Start
  <<DriverName>>SimpleNetworkStop,            // Stop
  <<DriverName>>SimpleNetworkInitialize,      // Initialize 
  <<DriverName>>SimpleNetworkReset,           // Reset
  <<DriverName>>SimpleNetworkShutdown,        // Shutdown
  <<DriverName>>SimpleNetworkReceiveFilters,  // ReceiveFilters
  <<DriverName>>SimpleNetworkStationAddress,  // StationAddress 
  <<DriverName>>SimpleNetworkStatistics,      // Statistics
  <<DriverName>>SimpleNetworkMCastIpToMac,    // MCastIpToMac 
  <<DriverName>>SimpleNetworkNvData,          // NvData
  <<DriverName>>SimpleNetworkGetStatus,       // GetStatus
  <<DriverName>>SimpleNetworkTransmit,        // Transmit
  <<DriverName>>SimpleNetworkReceive,         // Receive
  NULL,                                       // WaitForPacket
  NULL                                        // Mode 
};

EFI_STATUS
EFIAPI
<<DriverName>>SimpleNetworkStart (
  IN EFI_SIMPLE_NETWORK_PROTOCOL              *This
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleNetworkStop (
  IN EFI_SIMPLE_NETWORK_PROTOCOL              *This
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleNetworkInitialize (
  IN EFI_SIMPLE_NETWORK_PROTOCOL              *This,
  IN UINTN                                    ExtraRxBufferSize,  OPTIONAL
  IN UINTN                                    ExtraTxBufferSize   OPTIONAL
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleNetworkReset (
  IN EFI_SIMPLE_NETWORK_PROTOCOL              *This
  IN BOOLEAN                                  ExtendedVerification
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleNetworkShutdown (
  IN EFI_SIMPLE_NETWORK_PROTOCOL              *This
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleNetworkReceiveFilters (
  IN EFI_SIMPLE_NETWORK_PROTOCOL               *This,
  IN UINT32                                    Enable,
  IN UINT32                                    Disable,
  IN BOOLEAN                                   ResetMCastFilter,
  IN UINTN                                     MCastFilterCnt,    OPTIONAL
  IN EFI_MAC_ADDRESS                           *MCastFilter       OPTIONAL
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleNetworkStationAddress (
  IN EFI_SIMPLE_NETWORK_PROTOCOL               *This,
  IN BOOLEAN                                   Reset,
  IN EFI_MAC_ADDRESS                           *New    OPTIONAL
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleNetworkStatistics (
  IN EFI_SIMPLE_NETWORK_PROTOCOL               *This,
  IN BOOLEAN                                   Reset,
  IN OUT UINTN                                 *StatisticsSize,   OPTIONAL
  OUT EFI_NETWORK_STATISTICS                   *StatisticsTable   OPTIONAL
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleNetworkMCastIpToMac (
  IN EFI_SIMPLE_NETWORK_PROTOCOL               *This,
  IN BOOLEAN                                   IPv6,
  IN EFI_IP_ADDRESS                            *IP,
  OUT EFI_MAC_ADDRESS                          *MAC
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleNetworkNvData (
  IN EFI_SIMPLE_NETWORK_PROTOCOL              *This,
  IN BOOLEAN                                  ReadWrite,  
  IN UINTN                                    Offset
  IN UINTN                                    BufferSize,
  IN OUT VOID                                 *Buffer
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleNetworkGetStatus (
  IN EFI_SIMPLE_NETWORK_PROTOCOL              *This,
  OUT UINT32                                  *InterruptStatus,  OPTIONAL
  OUT VOID                                    **TxBuf            OPTIONAL
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleNetworkTransmit (
  IN EFI_SIMPLE_NETWORK_PROTOCOL              *This,
  IN UINTN                                    HeaderSize,
  IN UINTN                                    BufferSize,
  IN VOID                                     *Buffer,
  IN EFI_MAC_ADDRESS                          *SrcAddr,   OPTIONAL
  IN EFI_MAC_ADDRESS                          *DestAddr,  OPTIONAL
  IN UINT16                                   *Protocol   OPTIONAL
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>SimpleNetworkReceive (
  IN     EFI_SIMPLE_NETWORK_PROTOCOL          *This,
  OUT    UINTN                                *HeaderSize, OPTIONAL
  IN OUT UINTN                                *BufferSize,
  OUT    VOID                                 *Buffer,
  OUT    EFI_MAC_ADDRESS                      *SrcAddr,    OPTIONAL
  OUT    EFI_MAC_ADDRESS                      *DestAddr,   OPTIONAL
  OUT    UINT16                               *Protocol    OPTIONAL
  )
{
}
```
