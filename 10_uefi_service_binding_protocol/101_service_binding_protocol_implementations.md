<!--- @file
  10.1 Service Binding Protocol Implementations

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

## 10.1 Service Binding Protocol Implementations

The implementation of the Service Binding Protocol for a specific driver is
typically found in the file `<<DriverName>>`.c. This file typically contains
the following:
* Add global variable for the `EFI_SRVICE_BINDING_PROTOCOL` instance to
  `<<DriverName>>.c`.
* Implementation of the `CreateChild()` service.
* Implementation of the `DestroyChild()` service.
* If the UEFI Driver follows the UEFI Driver Model, install all the Service
  Binding Protocol in the Driver Binding Protocol `Start()` function.
* If the UEFI Driver follows the UEFI Driver Model, uninstall all the Service
  Binding Protocol in the Driver Binding Protocol `Stop()` function.
* If the UEFI Driver is a Service Driver, install all the Service Binding
  Protocol in the driver entry point.
* If the UEFI Driver is a Service Driver that supports the unload feature, then
  uninstall all the Service Binding Protocol in the `Unload()` function.

The example below shows the protocol interface structure for the Service
Binding Protocol for reference. It is composed of the two services called
`CreateChild()` and `DestroyChild()`.

###### Example 124-Service Binding Protocol

```c
typedef struct _EFI_SERVICE_BINDING_PROTOCOL
  EFI_SERVICE_BINDING_PROTOCOL;
  
///
/// The EFI_SERVICE_BINDING_PROTOCOL provides member functions to create
/// and destroy child handles. A driver is responsible for adding
/// protocols to the child handle in CreateChild() and removing protocols
/// in DestroyChild(). It is also required that the CreateChild()
/// function opens the parent protocol BY_CHILD_CONTROLLER to establish
/// the parent-child relationship, and closes the protocol in
/// DestroyChild(). The pseudo code for CreateChild() and DestroyChild()
/// is provided to specify the required behavior, not to specify the
/// required implementation. Each consumer of a software protocol is
/// responsible for calling CreateChild() when it requires the protocol
/// and calling DestroyChild() when it is finished with that protocol.
///
struct _EFI_SERVICE_BINDING_PROTOCOL {
  EFI_SERVICE_BINDING_CREATE_CHILD CreateChild;
  EFI_SERVICE_BINDING_DESTROY_CHILD DestroyChild;
};
```
