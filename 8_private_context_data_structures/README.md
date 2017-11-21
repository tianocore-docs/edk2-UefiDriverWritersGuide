<!--- @file
  8 Private Context Data Structures

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

# 8 Private Context Data Structures

UEFI drivers managing more than one controller need to be designed with
reentrancy in mind. This means that global variables should not be used to
track information about individual controllers. Instead, data structures should
be allocated with the UEFI memory services for each controller, and those data
structures should contain all the information that the driver requires to
manage each individual controller.

This chapter introduces some object-oriented programming techniques that can be
applied to drivers managing controllers. These techniques can simplify driver
design and implementation. The concept of a private context data structure
containing all the information required to manage a controller is introduced.
This data structure contains the public data fields, public services, private
data fields, and private services a UEFI driver may require to manage a
controller.

Some categories of UEFI drivers do not require the use of these data
structures. If a UEFI driver only produces a single protocol, or it manages, at
most, one device, the techniques presented here are not required. An
initializing driver does not produce any services and does not manage any
devices, so it does not use this technique. A service driver that produces a
single protocol and does not manage any devices does not likely use this
technique. A root bridge driver that manages a single root bridge device does
not likely use this technique, but a root bridge driver that manages more than
one root bridge device may use this technique.

Finally, all UEFI drivers that follow the UEFI driver model should use this
technique. Even if the driver writer is convinced that the UEFI driver manages
only a single device in a platform, this technique should still be used because
it simplifies the process of updating the driver to manage more than one
device. The driver writer should make as few device and platform assumptions as
possible when designing a new driver.

Implementations of Hybrid drivers that follow the UEFI Driver Model may define
two different private context data structures―one for the bus controller and
another one for the child controllers it produces.

It is possible to use other techniques to track the information required to
manage multiple controllers in a re-entrant-safe manner, but those techniques
likely require more overhead in the driver itself to manage this information.
The techniques presented here are intended to produce small driver executables.
These techniques are used throughout the drivers in EDK II.
