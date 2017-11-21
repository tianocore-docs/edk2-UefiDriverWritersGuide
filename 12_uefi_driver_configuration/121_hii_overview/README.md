<!--- @file
  12.1 HII overview

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

## 12.1 HII overview

A UEFI driver is not allowed to directly invoke a platform's forms browser.
Instead, a UEFI Driver provides sets of forms (the equivalent of Web pages) to
HII. If and when the forms browser is run, the web pages are displayed and
configuration takes place. The benefits of using forms instead of the Simple
Text Input Protocol and Simple Text Output Protocols include that:
* The forms allow use of a pre-existing GUI―the system already has a browser.
  This means a UEFI Driver can take advantage of the browser's features and
  allows the system to have a more consistent look and feel for the user.
* The forms are device-neutral. The browser can manage the forms appropriately
  for any device―for instance, a smart phone versus a laptop.
* The forms allow for remote configuration of devices. Instead of requiring
  that the user to go the physical machine and press (for example) Ctrl-Alt-F4,
  the text input can be handled remotely via the browser.

HII is designed to enable support of the data structures required to support
fully localized text and graphical user interfaces to the user. This consists
of four types of support:

1. `Keyboard:` HII supports keyboard mappings―the keyboard reflects the
   language the user is expecting to use. For example, French and English
   mappings differ in the Q, A, and Z keys. Keyboards simply return the location of the key, not its Unicode value. The HII support for key mapping allows translation from key location to Unicode value. There is no support for IMEs.

2. `Fonts:` HII supports fonts for the approximately 37,000 Unicode printable
   characters in Unicode UCS-2 The system carries the Latin-1 (Western
   European) character set. Other characters must be provided if they are to be
   displayed. HII also supports narrow and wide characters to support
   logographic languages (such as Chinese, Japanese, and Korean).

3. `Strings:` HII expects strings to be compressed Unicode stored by language.
   Drivers reference strings by IDs, which requires less storage. The actual
   string selected is defined by the ID and by the selected language.

4. `Forms:` HII defines its own forms language known as IFR. Although similar
   to web-based forms languages (such as HTML), IFR is stored in binary. IFR
   supports the usual tags, headers, and so on, found in a normal forms markup
   language. However, IFR also has special support for items common to
   configuration including multiple defaults and context-sensitive help. Unlike
   most forms languages, HII refers to strings via ID, so the same form can be
   used for multiple languages. HII also supports a rich set of operations for
   validating results. If all else fails, HII can reference callbacks into the
   submitting driver's code.

**********
**Note:** _IFR is a variable-length encoding of HTML-like tags. While experts
can write in this language (a bit like using DBs to write assembly language),
most developers use a high level language known as VFR. VFR compiles into IFR
and makes writing UEFI forms similar to writing HTML. The EDK II build tools
provide full support for VFR along with a VFR to IFR compiler._
**********

HII data is stored in a central HII database dynamically created upon each
reboot. HII protocols allow for a driver's HII data to be submitted,
manipulated, and extracted.

Configuration in a UEFI system is the province of a single setup browser.
Drivers submit their HII data to the HII protocols. The browser then parses
through the forms in the same way an internet browser would parse web data. The
setup browser communicates with the drivers to obtain current configuration
information and to provide updates when the session completes.
