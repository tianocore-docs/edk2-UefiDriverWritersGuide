<!--- @file
  A.3.1 _Usb2Hc.c File_

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

### A.3.1 _Usb2Hc.c File_

###### Example A-13-USB 2 Host Controller Protocol implementation template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#include `<<DriverName>>.h`

GLOBAL_REMOVE_IF_UNREFERENCED
EFI_USB2_HC_PROTOCOL g<<DriverName>>Usb2HostController = {
  <<DriverName>>Usb2HostControllerGetCapability,
  <<DriverName>>Usb2HostControllerReset,
  <<DriverName>>Usb2HostControllerGetState,
  <<DriverName>>Usb2HostControllerSetState,
  <<DriverName>>Usb2HostControllerControlTransfer,
  <<DriverName>>Usb2HostControllerBulkTransfer,
  <<DriverName>>Usb2HostControllerAsyncInterruptTransfer,
  <<DriverName>>Usb2HostControllerSyncInterruptTransfer,
  <<DriverName>>Usb2HostControllerIsochronousTransfer,
  <<DriverName>>Usb2HostControllerAsyncIsochronousTransfer, 
  <<DriverName>>Usb2HostControllerGetRootHubPortStatus,
  <<DriverName>>Usb2HostControllerSetRootHubPortFeature,
  <<DriverName>>Usb2HostControllerClearRootHubPortFeature,
  <<UsbSpecificationMajorRevision>>,
  <<UsbSpecificationMinorRevision>>
};

EFI_STATUS
EFIAPI
<<DriverName>>Usb2HostControllerGetCapability (
  IN  EFI_USB2_HC_PROTOCOL  *This,
  OUT UINT8                 *MaxSpeed,
  OUT UINT8                 *PortNumber,
  OUT UINT8                 *Is64BitCapable
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>Usb2HostControllerReset (
  IN EFI_USB2_HC_PROTOCOL   *This,
  IN UINT16                 Attributes
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>Usb2HostControllerGetState (
  IN  EFI_USB2_HC_PROTOCOL  *This,
  OUT EFI_USB_HC_STATE      *State
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>Usb2HostControllerSetState (
  IN EFI_USB2_HC_PROTOCOL   *This,
  IN EFI_USB_HC_STATE       State
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>Usb2HostControllerControlTransfer (
  IN     EFI_USB2_HC_PROTOCOL                *This,
  IN     UINT8                               DeviceAddress,
  IN     UINT8                               DeviceSpeed,
  IN     UINTN                               MaximumPacketLength,
  IN     EFI_USB_DEVICE_REQUEST              *Request,
  IN     EFI_USB_DATA_DIRECTION              TransferDirection,
  IN OUT VOID                                *Data,                OPTIONAL
  IN OUT UINTN                               *DataLength,          OPTIONAL
  IN     UINTN                               TimeOut,
  IN     EFI_USB2_HC_TRANSACTION_TRANSLATOR  *Translator,
  OUT    UINT32                              *TransferResult
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>Usb2HostControllerBulkTransfer (
  IN     EFI_USB2_HC_PROTOCOL                *This,
  IN     UINT8                               DeviceAddress,
  IN     UINT8                               EndPointAddress,
  IN     UINT8                               DeviceSpeed,
  IN     UINTN                               MaximumPacketLength,
  IN     UINT8                               DataBuffersNumber,
  IN OUT VOID                                *Data[EFI_USB_MAX_BULK_BUFFER_NUM],
  IN OUT UINTN                               *DataLength,
  IN OUT UINT8                               *DataToggle,
  IN     UINTN                               TimeOut,
  IN     EFI_USB2_HC_TRANSACTION_TRANSLATOR  *Translator,
  OUT    UINT32                              *TransferResult
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>Usb2HostControllerAsyncInterruptTransfer (
  IN     EFI_USB2_HC_PROTOCOL                *This,
  IN     UINT8                               DeviceAddress,
  IN     UINT8                               EndPointAddress,     IN UINT8 DeviceSpeed
  IN     UINTN                               MaxiumPacketLength,
  IN     BOOLEAN                             IsNewTransfer,
  IN OUT UINT8                               *DataToggle,
  IN     UINTN                               PollingInterval,     OPTIONAL
  IN     UINTN                               DataLength,          OPTIONAL
  IN     EFI_USB2_HC_TRANSACTION_TRANSLATOR  *Translator,         OPTIONAL
  IN     EFI_ASYNC_USB_TRANSFER_CALLBACK     CallBackFunction,    OPTIONAL
  IN     VOID                                *Context             OPTIONAL
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>Usb2HostControllerSyncInterruptTransfer (
  IN     EFI_USB2_HC_PROTOCOL                *This,
  IN     UINT8                               DeviceAddress,
  IN     UINT8                               EndPointAddress,
  IN     UINT8                               DeviceSpeed,
  IN     UINTN                               MaximumPacketLength,
  IN OUT VOID                                *Data,
  IN OUT UINTN                               *DataLength,
  IN OUT UINT8                               *DataToggle,
  IN     UINTN                               TimeOut,
  IN     EFI_USB2_HC_TRANSACTION_TRANSLATOR  *Translator,
  OUT    UINT32                              *TransferResult
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>Usb2HostControllerIsochronousTransfer (
  IN     EFI_USB2_HC_PROTOCOL                *This,
  IN     UINT8                               DeviceAddress,
  IN     UINT8                               EndPointAddress,
  IN     UINT8                               DeviceSpeed,
  IN     UINTN                               MaximumPacketLength,
  IN     UINT8                               DataBuffersNumber,
  IN OUT VOID                                *Data[EFI_USB_MAX_ISO_BUFFER_NUM],
  IN     UINTN                               DataLength,
  IN     EFI_USB2_HC_TRANSACTION_TRANSLATOR  *Translator,
  OUT    UINT32                              *TransferResult
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>Usb2HostControllerAsyncIsochronousTransfer (
  IN     EFI_USB2_HC_PROTOCOL                *This,
  IN     UINT8                               DeviceAddress,
  IN     UINT8                               EndPointAddress,
  IN     UINT8                               DeviceSpeed,
  IN     UINTN                               MaximumPacketLength,
  IN     UINT8                               DataBuffersNumber,
  IN OUT VOID                                *Data[EFI_USB_MAX_ISO_BUFFER_NUM],
  IN     UINTN                               DataLength,
  IN     EFI_USB2_HC_TRANSACTION_TRANSLATOR  *Translator,
  IN     EFI_ASYNC_USB_TRANSFER_CALLBACK     IsochronousCallBack,
  IN     VOID                                *Context                            OPTIONAL
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>Usb2HostControllerGetRootHubPortStatus (
  IN  EFI_USB2_HC_PROTOCOL  *This,
  IN  UINT8                 PortNumber,
  OUT EFI_USB_PORT_STATUS   *PortStatus
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>Usb2HostControllerSetRootHubPortFeature (
  IN EFI_USB2_HC_PROTOCOL   *This,
  IN UINT8                  PortNumber,
  IN EFI_USB_PORT_FEATURE   PortFeature
  )
{
}

EFI_STATUS
EFIAPI
<<DriverName>>Usb2HostControllerClearRootHubPortFeature (
  IN EFI_USB2_HC_PROTOCOL   *This,
  IN UINT8                  PortNumber,
  IN EFI_USB_PORT_FEATURE   PortFeature
  )
{
}
```
