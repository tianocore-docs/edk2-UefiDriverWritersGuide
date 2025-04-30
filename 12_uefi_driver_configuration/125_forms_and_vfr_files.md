<!--- @file
  12.5 Forms and VFR files

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

## 12.5 Forms and VFR files

Here is a sample, simplified VFR file. It declares a form set with one form and
uses a single variable store to retrieve and save configuration settings. The
form contains a title and four questions:
1. Allows a user to select one of two pre-defined values.
2. Allows the user to type in a string value.
3. Allows the user to type in a numeric value.
4. Allows the user to select a button to reset settings to defaults.

###### Example 143-Sample VFR file, simplified

```c
///** @file
//
// Sample Setup formset.
//
// Copyright (c) 2004 - 2010, Intel Corporation. All rights reserved.<BR>
// This program and the accompanying materials
// are licensed and made available under the terms and conditions of the BSD
// License which accompanies this distribution. The full text of the license may be
// found at http:
//opensource.org/licenses/bsd-license.php
//
// THE PROGRAM IS DISTRIBUTED UNDER THE BSD LICENSE ON AN "AS IS" BASIS,
// WITHOUT WARRANTIES OR REPRESENTATIONS OF ANY KIND, EITHER EXPRESS OR IMPLIED.
//
//**/

#include "NVDataStruc.h"

formset
  guid = FORMSET_GUID,
  title = STRING_TOKEN (STR_FORM_SET_TITLE), 
  help = STRING_TOKEN (STR_FORM_SET_TITLE_HELP),
  classguid = EFI_HII_PLATFORM_SETUP_FORMSET_GUID,

  //
  // Define a Buffer Storage (EFI_IFR_VARSTORE)
  //
  varstore DRIVER_SAMPLE_CONFIGURATION,           // This is the data structure type 
    varid = CONFIGURATION_VARSTORE_ID,            // Optional VarStore ID 
    name = MyIfrNVData,                           // Define referenced name in vfr 
    guid = FORMSET_GUID;                          // GUID of this buffer storage

  defaultstore MyStandardDefault,
    prompt = STRING_TOKEN (STR_STANDARD_DEFAULT_PROMPT),
    attribute = 0x0000;                           // Default ID: 0000 standard default

  defaultstore MyManufactureDefault,
    prompt = STRING_TOKEN (STR_MANUFACTURE_DEFAULT_PROMPT),
    attribute = 0x0001;                           // Default ID: 0001 manufacture default

  //
  // Define a Form (EFI_IFR_FORM)
  //
  form formid = 1, // Form ID
    title = STRING_TOKEN (STR_FORM1_TITLE);       // Form title 
    subtitle text = STRING_TOKEN(STR_SUBTITLE_TEXT);
    subtitle text = STRING_TOKEN (STR_SUBTITLE_TEXT2);

    //
    // Define oneof (EFI_IFR_ONE_OF)
    //
    oneof name = MyOneOf,                         // Define reference name for Question 
      varid = MyIfrNVData.MyBaseAddress,          // Use "DataStructure.Member" to 
      prompt = STRING_TOKEN(STR_ONE_OF_PROMPT), 
      help = STRING_TOKEN(STR_ONE_OF_HELP),
      //
      // Define an option (EFI_IFR_ONE_OF_OPTION)
      //
      option text = STRING_TOKEN (STR_ONE_OF_TEXT1), value = 0x0, flags = 0;
      option text = STRING_TOKEN (STR_ONE_OF_TEXT2), value = 0x1, flags = 0; 
      //
      // DEFAULT indicate that this option is to be marked with
      // EFI_IFR_OPTION_DEFAULT
      //
      option text = STRING_TOKEN (STR_ONE_OF_TEXT3), value = 0x2, flags = DEFAULT;
    endoneof;
  
    //
    // Define a string (EFI_IFR_STRING)
    //
    string varid = MyIfrNVData.MyStringData, 
      prompt = STRING_TOKEN (STR_MY_STRING_PROMPT),
      help = STRING_TOKEN (STR_MY_STRING_HELP), 
      flags = INTERACTIVE, 
      key = 0x1236, 
      minsize = 6, 
      maxsize = 40, 
    endstring;

    numeric varid = MyIfrNVData.MyHexData, 
      questionid = 0x1111,
      prompt = STRING_TOKEN (STR_DATA_HEX_PROMPT), 
      help = STRING_TOKEN (STR_NUMERIC_HELP),
      flags = DISPLAY_UINT_HEX | INTERACTIVE,     // Display in HEX format (if not
                                                  // specified, default is in decimal
                                                  // format) 
      minimum = 0, 
      maximum = 250, 
      default = 175, 
    endnumeric;

    resetbutton
      defaultstore = MyStandardDefault,
      prompt = STRING_TOKEN (STR_STANDARD_DEFAULT_PROMPT), 
      help = STRING_TOKEN (STR_STANDARD_DEFAULT_HELP), 
    endresetbutton;
  endform;
endformset;
```
