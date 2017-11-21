<!--- @file
  12.2 General steps for implementing HII functionality

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

## 12.2 General steps for implementing HII functionality

To include HII functionality in a driver, follow these general steps:
1. Decide if the user should be allowed to change configuration data.
2. Identify a set of options that can be changed. These options are stored in
   NVRAM. The NVRAM storage may be either local to the device being configured
   (recommended), or it may be global to the platform such as UEFI Variables
   (not recommended). UEFI Variables must be used only to store configuration
   data for UEFI Drivers integrated into a platform.
3. Define the C data structure for the configurable data. Drivers have
   flexibility on how they process configurable data. For example, data can be
   managed as name, value pairs or as a data structure. The full C data
   structure is defined in a .h file for the UEFI Driver usually in
   `<<DriverName>>`.h.
4. Determine the order of the questions presented to the user-for example, the
   order in which to present the fields and their values. For example, select
   on or off, yes or no, or enter a specific value, and so on. This information
   is typically stored in the file called `<<DriverName>>` .Vfr. The order in
   which the information is listed in the `<<DriverName>>`.Vfr file is the
   order in which each configurable field is displayed in the form.

   **********
   **TIP:** When designing questions, remember that the way the user sees the data
   may vary considerably depending on the device used. For example, it could vary
   from a few lines on a plasma display on the front panel of a home electronics
   device to a full, rich GUI interface on a remote console.
   **********

5. Define the strings for the form including the title of the form, help
   information for the form title; the titles for each configurable field and
   the help information (if any) for each configurable field. This information
   is typically stored in the `<<DriverName>>`.Uni. The example below shows a portion of the Unicode string file from a sample driver in the MdeModulePkg on the path MdeModulePkg/Universal/DriverSampleDxe.

###### Example 137-Example of a Unicode string file

```c
#langdef en-US "English"
#langdef fr-FR "Francais"
#string STR_FORM_SET_TITLE       #language en-US "Browser Testcase Engine"
                                 #language fr-FR "Browser Testcase Engine" 
#string STR_FORM_SET_TITLE_HELP  #language en-US "This is a sample driver which is used to test the browser
op-code operations. This is for development purposes and not to be distributed
in any form other than a test application. Here is a set of wide
\wideAAAAAAAAA\narrow and narrow AAA!"
                                 #language fr-FR "This is a sample driver which is used to test the browser
op-code operations. This is for development purposes and not to be distributed
in any form other than a test application. Here is a set of wide
\wideAAAAAAAAA\narrow and narrow AAA!"  
#string STR_FORM1_TITLE
                                 #language en-US "My First Setup Page"
                                 #language fr-FR "Mi Primero Arreglo P`᧩`na"`
#string STR_FORM2_TITLE          #language en-US "My Second Setup Page"
                                 #language fr-FR "Mi Segunda Paginaci᧩e la Disposici᧩uot;
#string STR_FORM3_TITLE          #language en-US "My Third Setup Page"
                                 #language fr-FR "Mi Tercera Paginaci᧩e la Disposici᧩uot;
#string STR_DYNAMIC_TITLE        #language en-US "My Dynamic Page"
                                 #language fr-FR "My Dynamic Page Spanish"
#string STR_SUBTITLE_TEXT        #language en-US "My subtitle text"
                                 #language fr-FR "Mi texto del subtlo"
#string STR_SUBTITLE_TEXT2       #language en-US " "
                                 #language fr-FR " "
#string STR_CPU_STRING           #language en-US "My CPU Speed is "
                                 #language fr-FR "My CPU Speed is "
#string STR_CPU_STRING2          #language en-US " "
                                 #language fr-FR " "
```

6\. Determine if the driver must be localized: Does it need to support more than
   one language? If so, the strings must be translated. Determine if the
   languages can be displayed using the Latin-1 character set (European). If
   not, obtain fonts for the characters in the languages the driver supports.

    At this point, a .uni file, a .vfr file, and a .h file have been produced. Typically, there is only one .uni file and one .vfr file per driver. More than one .uni file may be required if the driver presents multiple forms or menus. More than one .vfr file may be required in certain circumstances, for example, to simplify maintenance by holding an area of functionality in a separate .vfr file that changes often.

7\. Implement HII Config Access Protocol to retrieve and save configuration
   information associated with the HII forms. The implementation of the HII
   Config Access Protocol is typically found in the file HiiConfigAccess.c.
   _Appendix A_ contains a template for a HiiConfigAccess.c file for a UEFI
   Driver. The Config Access Protocol contains three services: ExtractConfig(),
   RouteConfig(), and DriverCallback(). The following example shows the
   definition of the HII Config Access Protocol for reference. When the HII
   setup browser is called, these functions are used to retrieve and store
   configuration setting as well as to retrieve default settings.

**********
**Note:** _This is still the init section. The driver has not attached to those
protocols yet._
**********

###### Example 138-Example of a Unicode string file

```c
typedef struct _EFI_HII_CONFIG_ACCESS_PROTOCOL EFI_HII_CONFIG_ACCESS_PROTOCOL;

///
/// This protocol provides a callable interface between the HII and
/// drivers. Only drivers which provide IFR data to HII are required
/// to publish this protocol.
///
struct _EFI_HII_CONFIG_ACCESS_PROTOCOL {
  EFI_HII_ACCESS_EXTRACT_CONFIG ExtractConfig;
  EFI_HII_ACCESS_ROUTE_CONFIG RouteConfig;
  EFI_HII_ACCESS_FORM_CALLBACK Callback;
} ;

extern EFI_GUID gEfiHiiConfigAccessProtocolGuid;
```

8\. Register all the packages from the driver entry point following the example
   in _Chapter 7_ on adding HII packages.

9\. If the UEFI Driver does not follow the UEFI Driver Model, install the HII
   Config Access Protocol from the driver entry point following the example in
   _Chapter 7_. If the UEFI Driver does follow the UEFI Driver Model, the HII
   Config Access Protocol is installed in the Driver Binding Protocol Start()
   function on each handle the UEFI Driver manages and provides configuration.

At this point, the driver's init part is done. When the form is displayed to
the user, the calls to the HII Config Access Protocol are made to retrieve and
save configuration settings. It is up to the implementation of the HII Config
Access Protocol to store configuration settings in NVRAM so they are available
the next time the platform boots.
