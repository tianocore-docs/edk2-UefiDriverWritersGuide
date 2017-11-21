<!--- @file
  22.3 Simple Text Output Protocol Implementation

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

## 22.3 Simple Text Output Protocol Implementation

The implementation of the Simple Text Output Protocol is typically found in the
file `SimpleTextOutput.c'. Appendix A contains a template for a SimpleTextOutput.c file for a UEFI Driver. The list of tasks to implement the Simple Text Output Protocol is as follows:
* Add global variable for the `EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL` instance to
  `SimpleTextOutput.c`.
* Add global variable for the `EFI_SIMPLE_TEXT_OUTPUT_MODE` structure to
  `SimpleTextOutput.c`.
* Implement the Simple Text Output Protocol services in `SimpleTextInput.c`.

The example below shows the protocol interface structure for the Simple Text
Output Protocol for reference. This protocol is composed of nine services and a
pointer to a _Mode_ structure.

###### Example 225-Simple Text Output Protocol

```c
typedef struct _EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL;

///
/// The SIMPLE_TEXT_OUTPUT protocol is used to control text-based output devices.
/// It is the minimum required protocol for any handle supplied as the ConsoleOut
/// or StandardError device. In addition, the minimum supported text mode of such
/// devices is at least 80 x 25 characters.
///
struct _EFI_SIMPLE_TEXT_OUTPUT_PROTOCOL {
  EFI_TEXT_RESET Reset;
  
  EFI_TEXT_STRING OutputString;
  EFI_TEXT_TEST_STRING TestString;
  
  EFI_TEXT_QUERY_MODE QueryMode;
  EFI_TEXT_SET_MODE SetMode;
  EFI_TEXT_SET_ATTRIBUTE SetAttribute;
  
  EFI_TEXT_CLEAR_SCREEN ClearScreen;
  EFI_TEXT_SET_CURSOR_POSITION SetCursorPosition;
  EFI_TEXT_ENABLE_CURSOR EnableCursor;
  
  ///
  /// Pointer to SIMPLE_TEXT_OUTPUT_MODE data.
  ///
  EFI_SIMPLE_TEXT_OUTPUT_MODE *Mode;
};
```
