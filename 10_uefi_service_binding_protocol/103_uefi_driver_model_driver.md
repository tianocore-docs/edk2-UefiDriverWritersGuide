<!--- @file
  10.3 UEFI Driver Model Driver

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

## 10.3 UEFI Driver Model Driver

If the UEFI Driver follows the UEFI Driver Model, the Service Binding Protocol
is installed in the Driver Binding Protocol `Start()` function and uninstalled
in the Driver Binding Protocol `Stop()` function. This use case is covered in detail in the
Service Binding Protocol section of the _UEFI Specification_ and includes
pseudo-code for implementations of the `CreateChild()` and `DestroyChild()`
services. The EDK II also provides the following complete implementations of
the Service Binding Protocol in drivers that follow the UEFI Driver Model:
* `MdeModulePkg\Universal\Network\MnpDxe`
* `MdeModulePkg\Universal\Network\ArpDxe`
* `MdeModulePkg\Universal\Network\Ip4Dxe`
* `NetworkPkg\Ip6Dxe`
* `MdeModulePkg\Universal\Network\Tcp4Dxe`
* `NetworkPkg\TcpDxe`
* `MdeModulePkg\Universal\Network\Udp4Dxe`
* `NetworkPkg\Udp6Dxe`
* `MdeModulePkg\Universal\Network\Mtftp4Dxe`
* `NetworkPkg\Mtftp6Dxe`
* `MdeModulePkg\Universal\Network\Dhcp4Dxe`
* `NetworkPkg\Dhcp6Dxe`
