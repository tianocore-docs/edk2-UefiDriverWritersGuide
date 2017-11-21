<!--- @file
  12.3.1 HII Database Protocol and HII String Protocol

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

### 12.3.1 HII Database Protocol and HII String Protocol

Use the database protocol to submit the package of strings, fonts, forms, and
so on, to the HII database. It is the most important of the HII protocols.
Because the package is created at build time, and most of the package does not
change, the driver does not have to call much later. _This can significantly
speed up boot time._

The strings protocol allows the general purpose forms to adapt to the
configuration of a specific platform. This includes configuration information
typed in by the user. The forms themselves are created by the VFR during build.

Basically, the string and database protocols facilitate database and string
management. The browser simply gets things out of the database after the driver
uses the set functions to put data into the database. The browser doesn't need
to know how to parse the database or even know how strings are stored; it needs
to know only how to parse the forms.

The EDK II provides a library, in the `MdeModulePkg`, called `HiiLib` that
provides helper functions to simplify the use of the HII Database and HII
String protocols. It also provides services to dynamically generate forms.

#### 12.3.1.1 HII Database Protocol

HII data is contained in HII packages. For example, A driver might have a
string package, a form package, and a small font package. HII supports package
lists as a way to combine HII packages to create a single data structure for
all the user interface HII data necessary for the driver. Rather than requiring
the driver to split the packs up to, for example, provide the string pack to
the string protocol and the font pack to the font protocol, the HII Database
Protocol consumes the entire package list and portions it out to the various
parts of the HII database. The package list format is described in the Human
Interface Infrastructure Overview chapter of the _UEFI Specification_.

When a package list is submitted to the database (via NewPackageList), an ID,
known as an HII handle, is associated with the data. This handle is required to
manipulate the pack list's data to ensure uniqueness. For example, if two
drivers submit string packs to the database, each have a string with an ID of 1
but they are different. The handle indicates which string with an ID of 1 to
access.

One parameter to NewPackageList deserves special attention: _DriverHandle_. The
driver handle indicates the handle on which the driver has put an instance of
the CONFIGURATION_ACCESS_PROTOCOL. This protocol is used to obtain ("extract") the
current configuration of a driver and to provide new configurations to it.

UpdatePackageList allows a driver to associate more than one package list with
the same handle. This may simplify complex configurations by splitting the
package into a common piece and additional configurations depending upon the
cards SKU.

The Database protocol also supports methods to extract pieces from the database
up to and including the entire database as well as ListPackageLists and
ExportPackageLists. These functions are rarely useful for a driver but are the
mechanisms by which the system places the HII data into the system table and
also how the Setup browser obtains the data used to present its screens. The
database protocol also supports notification functions for consumers of
database data so they can determine if new packages have been added or existing
ones removed.

Questions commonly asked include: Why are there individual protocols for some
package types? Why isn't there a single protocol? The main reason is that the
number of functions required became unwieldy. A secondary reason is that, for
some smaller implementations, subsets of HII could be implemented. In reality
this has not occurred.

The keyboard packages were judged as being simple enough to leave in the
database protocol. Keyboards are abstracted using a data structure per key. Each data structure
defines the key code to which the data structure refers, as well as the
unmodified Unicode weight and the weights when modified with Shift, Alt, and
Shift + Alt. Only the keys that vary from the standard US English layout need
be specified. Certain keys, such as NumLock, may also be assigned special
functions.

#### 12.3.1.2 HII String Protocol

The String Protocol consumes string packs. It also allows manipulation of
strings already in the database, even if they were submitted via the database
protocol.

It is quite common for a driver to need to manipulate certain strings when its
data is in the HII database. Consider the case of a media card with attached
mass storage devices. When the driver for the media card is created, the
identification data of the mass storage devices attached aren't known. That
data is derived when the card's driver is invoked, generally at `Start()`.

If the driver is to provide the mass storage device types to the setup browser,
it is common to allocate empty strings so the build allocates string IDs to the
strings. The driver can then parse the string pack to modify strings updating
them with the drive id data itself and then submit the string pack. This is
complex and tedious because the string packs are stored to be space efficient,
not to be easily accessible. The String protocol already knows how to parse the
string pack, however, and does provide methods to modify strings by ID. This
makes the job of updating strings for dynamically derived data an easy one.
Simply submit the string packs to the database, then modify the few strings
that change dynamically. Blank strings can be checked for in IFR so empty
channels don't have to be displayed.

#### 12.3.1.3 Adding data to the HII database at boot time

There is more than one way to add information to the database. A crude way of
adding information to the HII database is by using individual protocols to
specify the fonts, strings, and forms. A better way is to use the HII Database
Protocol. This protocol provide services to register the strings pack, fonts
pack, forms pack, and so on, all at once. Because most of the package is static
data, the driver does not have to do much work later during boot.

**********
**Note:** _If the VFR compiler is used as part of the build, the package
created may be published with this protocol._
**********

Also, note that the database is not complete at build time. The driver cannot
know all the data it needs about the end-user's specific system hardware or
other devices connected to the hardware. For example, the driver can't know a
specific platform's MAC address at build time, which specific mass storage
devices are attached via SCSI, each mass storage device's version information,
and so on. That type of information is acquired during setup. During setup or
boot, the package for the HII database must be updated.

Although data may be modified before being submitted to the database, that
process is both difficult and convoluted. Use the `SetString()` function in the
HII String Protocol instead.

For configurable data, or for data not available at build time, use a question
mark in the package for each of the blank fields. During boot, the driver
requests that information. Use the set string functionality of the HII String
Protocol to specify the ID of the new package list and update the database with
the new string from the build file.

**********
**Note:** _If driver A creates a package list for the database, and another
driver B creates another package list for the database, driver A's string #12
is not the same as driver B's string #12._
**********

#### 12.3.1.4 Update the database via the byte offset of a configurable field

To modify a form after build-time, include a comment line (a macro for the VFR
compiler) in the form's source code. The comment line does not generate code in
the form. It simply indicates the byte offset of the value which does change
the platformspecific information. The driver does not need to know how to parse
the whole form to find that value. Instead, a driver can use the offset to find
out where to edit the form.

Take the example of a SCSI driver with 2 drives specified as the default. In
this example, the end-user platform actually has 3 drives. The driver searches
for the appropriate comment to find the offset and the compiler tells the
driver that the description of the logical unit is at line 437 The driver goes
to that location, adds new forms data for the third drive and "slides" the rest
of the configuration forms down. Essentially, new data is inserted into a newly
created hole. Because the Internal Forms Language (IFR) is
decision-independent, there are no fixed addresses in the code so data may be
moved from one location to another relatively easily. The IFR also uses names
for references, not pointers. For example, if 20 bytes of data need to be added
at location 437, the 20 bytes can be copied into the new form.

**********
**Note:** _The driver can do a get operation on the whole form or on just the
string. The driver can do a get operation on the string because it uses the
existing infrastructure (the platform's browser and other tools), which already
know how to parse the database to find the appropriate data._
**********

#### 12.3.1.5 Using strings to create forms as-needed

Use strings to create forms as needed. For example, most of the time, a SCSI
has only 2 drives, but could have up to 8 Instead of creating a static form
with 8 fields, and only 2 filled at boot time, a form with the 2 required
fields can be created dynamically. The other 6 unused fields would not be
displayed until they are actually needed.

#### 12.3.1.6 Using strings to modify forms

In general, about 80% of any given form is static and common across the
system's hardware. The other 20% is specific to that platform.

When adding information after build, it is sometimes easier to simply update a
form. Other times it's easier to create a new form and turn it in. In general,
a new form should be created if 70-80% of the information is new or has changed.

The VFR programming language explains how to work with forms and includes tips
and suggestions for modifying forms.

#### 12.3.1.7 HII Database Protocol with Export Package List

The HII Database Protocol provides a service to export all registered packages
into an Export Package List. This includes packages registered by all UEFI
Drivers. The Export Package List is not typically used by UEFI Drivers
themselves. Instead, its purpose is to provide a single interface for external
entities to extract the data needed to configure the system remotely.

**********
**Note:** _Programs that perform remote configuration do not have access to
callbacks so questions related to callbacks are not visible remotely._ Requests
to read and write configuration data are routed to HII Config Access Protocol
instances.
**********
