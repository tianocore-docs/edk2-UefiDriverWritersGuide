<!--- @file
  12.4.2 Specifying supported languages

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

### 12.4.2 Specifying supported languages

The HII String Protocol allows strings and tokens to be used to specify the
supported languages for a driver. The strings themselves are defined in a
separate string file. That file is then published to the HII Database.

The string file must have at least one language definition and at least one
string. If there is only one language specified, that language is the default.
If more than one language is specified, then the first language listed is
always the default language.

**********
**Note:** _It is possible that no languages supported by the system are
supported by the driver. In this case the browser selects the default language
and proceeds. It is important to use the secondary language feature in HII to
describe alternate languages to provide maximum flexibility for a set of
strings._
**********

The following snippet from a Unicode string file shows American English (en-US)
as the default language because it is first in the list. The string file
includes support for two additional languages, French-Canadian (fr-CA), and
British English (en-UK).

###### Example 142-Unicode string file with support for multiple languages

```c
#langdef en-US "English"
#langdef fr-FR "Francais"
#langdef en-UK "British"

#string STR_INV_FORM_SET_TITLE #language en-US "ABC Information Sample"
                   #language fr-FR "Mi motor Espade arreglo"
                   #language en-UK "ABC Information Sample"
```

**********
**Note:** _It costs the driver almost no processing time to support multiple
languages because language selection is determined at the system level.
However, adding support for multiple languages with additional strings and
tokens can increase the size of the driver slightly. Adding support for many
languages (for example, 100 or more) could increase the size of the driver more
significantly._
**********
