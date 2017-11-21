<!--- @file
  22.2 Simple Text Input Protocol Implementation

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

## 22.2 Simple Text Input Protocol Implementation

The implementation of the Simple Text Input Protocols is typically found in the
file `SimpleTextInput.c`. Appendix A contains a template for a `SimpleTextInput.c` file for a UEFI Driver. The list of tasks to implement the Simple Text Input Protocols is as follows:
* Add global variable for the `EFI_SIMPLE_TEXT_INPUT_PROTOCOL` instance to
  `SimpleTextInput.c`. - Add global variable for the
  `EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL` instance to `SimpleTextInput.c`.
* Implement the `Reset()` and `ReadKeyStroke()`services in `SimpleTextInput.c`
  that is shared between the Simple Text Input Protocol and the Simple Text
  Input Ex Protocol.
* Implement the `SetState()`, `RegisterKeyNotify()`and `UnregisterKeyNotify()`
  services in `SimpleTextInput.c` for the Simple Text Input Ex Protocol.
* Implement notification function for the _WaitForKey_ and _WaitForKeyEx_
  events in `SimpleTextInput.c` that is shared between the Simple Text Input
  Protocol and the Simple Text Input Ex Protocol.
* Create _WaitForKey_ and _WaitForKeyEx_ events before the Simple Input
  Protocols are installed into the Handle Database.
* If a device does not buffer keystrokes, or the buffer is very small, a timer
  event may be required to periodically read contents from a keyboard buffer.

_Example 223_, following, shows the protocol interface structure for the Simple
Text Input Protocol and _Example 224_, below that, shows the protocol interface
structure for the Simple Text Input Ex Protocol for reference. These two
protocols are composed of services and each has an `EFI_EVENT` that may be used
by the UEFI Boot Manager or UEFI Applications to determine if a keystroke has been pressed. The UEFI Boot
Services `WaitForEvent()` and `CheckEvent()` can be used to perform these
checks on the events specified by _WaitForKey_ and _WaitForKeyEx_.

###### Example 223-Simple Text Input Protocol

```c
typedef struct _EFI_SIMPLE_TEXT_INPUT_PROTOCOL EFI_SIMPLE_TEXT_INPUT_PROTOCOL;

///
/// The EFI_SIMPLE_TEXT_INPUT_PROTOCOL is used on the ConsoleIn device.
/// It is the minimum required protocol for ConsoleIn.
///
struct _EFI_SIMPLE_TEXT_INPUT_PROTOCOL {
  EFI_INPUT_RESET Reset;
  EFI_INPUT_READ_KEY ReadKeyStroke;
  ///
  /// Event to use with WaitForEvent() to wait for a key to be available
  ///
  EFI_EVENT WaitForKey;
};
```

###### Example 224-Simple Text Input Ex Protocol

```c
typedef struct _EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL
  EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL;
  
///
/// The EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL is used on the ConsoleIn
/// device. It is an extension to the Simple Text Input protocol
/// which allows a variety of extended shift state information to be
/// returned.
///
struct _EFI_SIMPLE_TEXT_INPUT_EX_PROTOCOL {
  EFI_INPUT_RESET_EX Reset;
  EFI_INPUT_READ_KEY_EX ReadKeyStrokeEx;
  ///
  /// Event to use with WaitForEvent() to wait for a key to be available.
  ///
  EFI_EVENT WaitForKeyEx;
  EFI_SET_STATE SetState;
  EFI_REGISTER_KEYSTROKE_NOTIFY RegisterKeyNotify;
  EFI_UNREGISTER_KEYSTROKE_NOTIFY UnregisterKeyNotify;
};
```
