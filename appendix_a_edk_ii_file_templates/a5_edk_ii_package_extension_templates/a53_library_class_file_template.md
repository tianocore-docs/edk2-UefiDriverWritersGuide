<!--- @file
  A.5.3 _Library Class File Template_

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

### A.5.3 _Library Class File Template_

Library Classes and their associated functions, defines, and data structures
are declared very similar to protocols and GUIDs. The difference is that
Library Classes are typically placed in a different subdirectory of an EDK II
package. The typical path to a library .h file is
`<<PackageName>>/Include/Library/<<LibraryName>>.h`. For example, all the
libraries classes defined by the MdePkg can be found in the EDK II
/MdePkg/Include/Library. When a new library class is defined and added to an
include directory of an EDK II package, the library class must also be added to
the [LibraryClasses] section of a package's .dec file. The .dec file is where
the mapping between the name of the library class and the path to the include
file for the library class is declared. Defining a new library class is not
commonly required when implementing a new UEFI Driver. If a UEFI Driver
implementation does require a new library class, then the new library class is
usually added to the same EDK II package that contains the UEFI Driver
implementation.

Example A-30 shows a template for adding a new library class to the
[LibraryClasses] section of an EDK II package .dec file. Following that,
Example A-31 shows the template for the .h files for a library class placed in
the include directory of an EDK II package. All public functions provided by a
library class must use the `EFIAPI` calling convention. The return type for a
library class function is not required to be `EFI_STATUS`. `EFI_STATUS` is only
shown in this template as an example.

###### Example A-30-Add Library Class to an EDK II package

```ini
[LibraryClasses]
  ## @libraryclass <<BriefDescription>>
  ##
  <<LibraryClassName>>|Include/Library/<<LibraryClassName>>.h
```

###### Example A-31-Library Class include file template

```c
/** @file
  <<BriefDescription>>
  <<DetailedDescription>>
  <<Copyright>>
  <<License>>
**/

#ifndef __<<LIBRARY_CLASS_NAME>>_H__
#define __<<LIBRARY_CLASS_NAME>>_H__

///
/// Library class public defines
///
///
/// Library class public structures/unions
///
///
/// Library class public functions
///
EFI_STATUS
EFIAPI
LibraryFunction1 (
  //
  // Additional function arguments here.
  //
  );
  
VOID
EFIAPI
LibraryFunction2 (
  //
  // Additional function arguments here.
  //
  );
  
UINT8
EFIAPI
LibraryFunction3 (
  //
  // Additional function arguments here.
  //
  );
  
#endif
```
