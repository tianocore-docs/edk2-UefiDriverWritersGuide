<!--- @file
  4.2.14 Store Configuration Data with Device

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

### 4.2.14 Store Configuration Data with Device

The configuration for a UEFI driver should be stored on the same field
replaceable unit (FRU) as the managed device. If a UEFI driver is stored on the
motherboard, then the driver's configuration information can be stored in UEFI
variables. If a UEFI driver is stored in an add-in card, then the driver's
configuration information should be stored in the NVRAM provided on the add-in
card.

#### 4.2.14.1 Benefits

This method ensures that it is possible to statically determine the maximum
configuration storage that is required for the FRU during FRU design. In
particular, if option cards stored their configuration in UEFI variables, the
amount of variable storage could not be statically calculated because it
generally is not possible to know the particular set of option cards installed
in a system ahead of time. The result would be that add-in cards could not be
used in otherwise functional systems due to lack of UEFI variable storage space.

Storing configuration data in the same FRU as the device reduces the amount of
stale data left in UEFI variables. If an option card stored its data in UEFI
variables and was then removed, there would be no automatic cleanup mechanism
to purge the UEFI variables associated with that card.

Storing configuration data in the same FRU as the device also ensures that the
configuration stays with the FRU. It enables centralized configuration of
add-in cards. For example, if an IT department is configuring 50 like systems,
it can configure all 50 in the same system and then disburse them to the
systems, rather than configuring each system separately. It can also maintain
preconfigured spares.
