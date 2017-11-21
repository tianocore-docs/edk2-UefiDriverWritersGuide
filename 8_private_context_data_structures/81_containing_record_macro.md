<!--- @file
  8.1 Containing Record Macro

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

## 8.1 Containing Record Macro

The containing record macro, called `CR()`, enables good object-oriented
programming practices. It returns a pointer to the structure using a pointer to
one of the structure's fields. Protocol producing UEFI drivers use this macro
to retrieve the private context data structure from a pointer to a produced
protocol interface. Protocol functions are required to pass in a pointer to the
protocol instance as the first argument to the function. C++ does this automatically, and the pointer to the object instance is called a _this_ pointer. Since UEFI drivers are written in C, a close
equivalent is implemented by requiring that the first argument of every
protocol function be the pointer to the protocol's instance structure called
"This." Each protocol function then uses the `CR()` macro to retrieve a pointer
to the private context data structure from this first argument called _This_.

The example below is the definition of the `CR()` macro from the EDK II library
`DebugLib`. The `CR()` macro is provided a pointer to the following:

* A field in a data structure

* The name of the field

It uses this information to compute the offset of the field in the data
structure and subtracts this offset from the pointer to the field. This
calculation results in a pointer to the data structure that contains the
specified field. `BASE_CR()` returns a pointer to the data structure that
contains the specified field. For debug builds, `CR()` also does an additional
check to verify a signature value. If the signature value does not match, an
`ASSERT()` message is generated and the system is halted or generates a
breakpoint. For production builds, the signature checks are typically disabled.
Most UEFI drivers define additional macros based on the `CR()` macro that
retrieves the private context data structure based on a _This_ pointer to a
produced protocol. These additional macros are typically given names that make
it easier to understand in the source code that the _This_ pointer is being
used to retrieve the private context data structure defined by the UEFI Driver.

###### Example 111-Containing record macro definitions

```c
/**
  Macro that calls DebugAssert() if the containing record does not have a matching signature. If the signatures matches, then a pointer to the data structure that contains a specified field of that data structure is returned. This is a lightweight method that hides information by placing a public data structure inside a larger private data structure and using a pointer to the public data structure to retrieve a pointer to the private data structure.
  If the data type specified by TYPE does not contain the field specified by Field, then the module will not compile.
  If TYPE does not contain a field called Signature, then the module will not compile.
  @param  Record         The pointer to the field specified by Field within a data structure of type TYPE.
  @param  TYPE           The name of the data structure type to return
                         This data structure must contain the field specified by Field.
  @param  Field          The name of the field in the data structure specified by TYPE to which Record points.
  @param  TestSignature  The 32-bit signature value to match.
**/

#if !defined(MDEPKG_NDEBUG)
  #define CR(Record, TYPE, Field, TestSignature)                     \
    (DebugAssertEnabled () &&                                        \
      (BASE_CR (Record, TYPE, Field)->Signature != TestSignature)) ? \
    (TYPE *)(_ASSERT (CR has Bad Signature), Record) :               \
    BASE_CR (Record, TYPE, Field)
#else
  #define CR(Record, TYPE, Field, TestSignature)                     \
    BASE_CR (Record, TYPE, Field)
#endif
```

The following example shows the definition of the `BASE_CR()` macro from the
EDK II that is used to implement the `CR()` macro above. The `BASE_CR()` macro
does not perform any signature checking or handle any error conditions. This
macro may be used with data structures that do not have a _Signature_ field.

###### Example 112-Containing record macro definitions

```c
/**
  Macro that returns a pointer to the data structure that contains a specified field of that data structure. This is a lightweight method to hide information by placing a public data structure inside a larger private data structure and using a pointer to the public data structure to retrieve a pointer to the private data structure.
  
  This function computes the offset, in bytes, of field specified by Field from the beginning of the data structure specified by TYPE. This offset is subtracted from Record, and is used to return a pointer to a data structure of the type specified by TYPE. If the data type specified by TYPE does not contain the field specified by Field, then the module will not compile.
  
  @param  Record  Pointer to the field specified by Field within a 
                  data structure of type TYPE.
  @param  TYPE    The name of the data structure type to return. This
                  data structure must contain the field specified by 
				  Field.
  @param  Field   The name of the field in the data structure specified 
                  by TYPE to which Record points.
				  
  @return         A pointer to the structure from one of it's elements.
**/

#define BASE_CR(Record, TYPE, Field)                                   \
  ((TYPE *)((CHAR8 *)(Record) - (CHAR8 *) &(((TYPE *) 0)->Field)))
```
