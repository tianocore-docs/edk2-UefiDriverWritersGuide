<!--- @file
  12.3.4 Rarely used HII protocols

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

### 12.3.4 Rarely used HII protocols

There are two rarely used HII protocols: HII Font Protocol, and the HII Image
Protocol. Though rarely used, understanding them is important

#### 12.3.4.1 HII Font Protocol

The HII Font Protocol provides functionality equivalent to the String Protocol
but manages fonts instead. Fonts consist of glyphs, bit-mapped representations
of characters. The characters are referred to by their Unicode _weight_, which
is to say their corresponding binary value. For example, weight 0x0030 is a "0"
(zero). A font is a series of glyphs bound together by name, size and similar
visual characteristics.

The default font is the system font, which is 8x16 and 16x16 (for wide
characters). Latin-1 characters in this standard font are provided by the
system firmware. If a driver uses other characters, including e.g. Chinese,
Korean, Hindi, Arabic, Hebrew, etc. A driver must provide all of the characters
it uses. The build tools determine the actual characters used. Other fonts are
identified by GUID.

**********
**TIP:** It is strongly recommended that the system font be used for reasons of
size and consistency.
**********

Unlike strings, fonts are not separated by handle. When a driver provides fonts
to the database, the new glyphs are merged with existing glyphs, provided that
they are the same font. This means the display of a driver's data may use a
different driver's font characters.

#### 12.3.4.2 HII Image Protocol

HII provides simple support for images like graphical pictures and simplistic
animation. There is no requirement for browsers to support graphics. The
browser in EDK II does not support graphics and most setup browsers do not
support graphics simply because of size requirements. The exception is for
splash screens (banners).
