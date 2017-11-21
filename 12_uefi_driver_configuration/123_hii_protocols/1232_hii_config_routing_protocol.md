<!--- @file
  12.3.2 HII Config Routing Protocol

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

### 12.3.2 HII Config Routing Protocol

The Configuration Routing Protocol is not used by UEFI Drivers. However, it is
important to understand its role in the configuration process. This protocol is
used by consumers of forms to determine the current configuration of the tags
(questions) associated with the forms and to change the configuration of the
corresponding data.

The data format for both output from the drivers and input into the drivers is
Unicode strings of ampersand separated name=value pairs. Each string is
associated with a particular form, and hence, a particular driver. Specific
name=value pairs at the start of a string of data associate the data with a
particular instance of a driver.

This format can seem a little cumbersome at times but does provide a common,
well defined mechanism to present the data. It is useful particularly in cases
where the configuration of the system is handled remotely. It is also useful in
cases where the same configuration data is applied to multiple systems, such as
when systems are initially received by an IT department.

The data provided by the driver includes the leading name=value pairs. The data
provided by the configuration program consists of a single string that may be
consumed by multiple drivers (hence the name multi-config string). The routing
protocol uses the leading name=value pairs to break-up the multi-config string
and to determine the correct consumer of each of the substrings. Each driver
receives only its own configuration data via the HII Config Access Protocol
described below.

The leading name=value pairs (all in upper case only) are:

* GUID - The GUID in the Setup Form associated with this data

* NAME - The name of the driver

* PATH - The binary device path to the driver's device

A UEFI Driver may describe not only the current configuration but also several
alternate configurations. Each alternate configuration is described by an
identifier and preceded by a name=value pair with the name ALTCFG and the value
indicating the alternate configuration in the Form. These are typically default
configurations.

A UEFI driver maps its configuration into an array that is also represented as
a C data structure. In this case, each configurable item is represented by
three consecutive name=value pairs:

* OFFSET―The byte offset into the structure of the item

* WIDTH―The number of bytes the item consumes

* VALUE―The current (or new) configuration of the item

Helper functions map the string into a memory array to be stored by the UEFI
Driver. A UEFI Driver may receive a request for only certain configuration values, in which case only the names (and not the = or value) are filled in. The driver must fill in the values for the requested names.

If a UEFI Driver receives a configuration string containing incorrect leading
name=value pairs, unknown names or out of bound values, the driver must reject
the configuration request. In other words, the driver always validates the
input string.

There is no requirement to include all name=value pairs in a configuration
change string. The configuration associated with all names not mentioned in the
string should not change. The UEFI Driver must ensure that the results of the
reconfiguration are valid.

A UEFI Driver must provide a name=value pair parser that is tolerant of
different formats of numbers―0ab, ab, and AB are all the same number.
Similarly, the parser must be tolerant of case changes in names―Fred=5, fred=5,
and FRED=5 should all be tolerated.

A UEFI Driver implementation of the HII Config Access Protocol must pay close
attention to the memory allocation and deallocation requirements of the HII
Config Access Protocol. Sometimes, the caller allocates the memory, other
times, the callee allocates the memory. See the EFI HII Configuration Access
Protocol section of the _UEFI Specification_ for more details.

#### 12.3.2.1 Remote configuration

Previously, even when configuration is local, every PC BIOS legacy option ROM
had to carry its own setup-this took up a lot of space. With HII, only the
platform needs to carry the browser. The driver carries only the package-the
fonts and strings that the browser doesn't know about. For drivers having a
significant amount of configuration, using HII functionality can help reduce
the driver's size by as much as 20% or 30%.

For example, a platform may require some configuration at runtime. Or a
platform may require remote configuration by an Information Technology (IT)
administrator at a remote server that allows them to configure some settings,
and send those settings back. In order to do this, the management application
typically wants the entire database of information. Such an application sends
that database off to the remote system, which does the configuration via its
own setup browser, then sends the data back. The management application
provides configuration changes to the platform that are routed back to the UEFI
Driver managing the device being configured. This means a driver can support
remote configuration without having to implement all the functions that the
browser and management application already provide.

**********
**Note:** _Configuration data, whether configuration is remote or local, does
not need to use callbacks. In fact, a remote browser ignores all the pieces of
a form involving callbacks. Once the configuration is on the end-user platform,
callbacks are functional again because they are on a local machine._
**********
