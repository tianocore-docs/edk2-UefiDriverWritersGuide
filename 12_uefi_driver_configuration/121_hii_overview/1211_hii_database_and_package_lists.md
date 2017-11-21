<!--- @file
  12.1.1 HII Database and Package Lists

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

### 12.1.1 HII Database and Package Lists

The HII database is built dynamically as the system boots. A UEFI Driver is
required to register lists of HII packages into the HII Database. A package
list is a list of packages providing different types of binary data. The data
types supported include font, string, image, keyboard, and forms data.

**********
**Note:** _The package could also contain some keyboard data but keyboard
layouts are typically outside the scope of a driver (and typically up to the
platform to determine). For example, keyboard data could represent the French
keyboard, a simplified set of Hiragana and Katakana characters for a Japanese
keyboard, and so on. The Unicode values of keyboard data are mapped to the
characters of each supported language and displayed to the screen._
**********

The goal of the package is to create a single form with multiple sets of
strings. For example, the goal for fonts is to create a single form with
multiple sets of strings, each set for a different supported language. The sets
of strings are published to the HII database, which also contains the strings,
fonts, and characters from other drivers. The setup browser can then access the
HII Database to display the forms in the appropriate language and font.

In general, data in a package is not modified after it is registered. For
example, data that probably won't change during configuration include the
questions that are presented to a user, the layout of the forms, the font list,
and so on.
