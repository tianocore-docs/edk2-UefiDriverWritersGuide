<!--- @file
  12.3.3 HII Config Access Protocol

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

### 12.3.3 HII Config Access Protocol

The HII Config Access Protocol is produced by the UEFI driver. It has three key functions that are published to the HII database. The first two functions are used by the Configuration Routing Protocol to extract data from drivers and to provide configuration back to the drivers. The format of the configuration is modeled after the CGI: A Unicode string of ampersand separated name = value pairs (x=1&y=2&z=3A). The names and values are specified in the forms. Only names are provided for extract requests. The driver cannot assume that all names in a form are present in a request―the caller may limit the entries to only those it needs.

Callbacks are the method by which the browser and driver directly communicate
with each other. The forms describe when to invoke callbacks and they provide
some context for the callback.

Use callbacks to update dynamic data, such as ambient temperature, fan speed,
etc. They should not be used to modify how items are displayed.

The following three key functions are published to the HII database:

* **ExtractConfig function:** This function is called by the HII engine at the
  beginning of a particular form. This function gives the driver a chance to
  perform tasks before the form is processed by the HII database engine. For
  example, the function could test the current NVRAM data structure to make
  sure it is not corrupt. This function also allows the browser to display
  current configuration information.

**********
**Note:** _The ExtractConfig function eliminates the need to use the previous,
tedious method of manually outputting to the console, reading strings back from
the console, and manually interpreting those strings._
**********

* **RouteConfig function:** This function allows the browser to obtain and change
  configuration information upon the exit of the form. It performs the final
  store and routes the appropriate data out to whoever needs it. For example,
  this function copies the current data back to the data structure in NVRAM.
  This function processes any changes that the user enters.

* **Callback function:** This function is called when a user makes changes. After
  the changes are saved, the original data structure is updated with the new
  settings.

**********
**Note:** _This is not a callback in the traditional sense. This function is
used by the browser to route data back to the appropriate driver so each driver
can process its own configuration._
**********

#### 12.3.3.1 Sample code for routing protocols

The following three examples show how the ExtractConfig, RouteConfig, and
Callback functions of the Config Access Protocol may be used.

###### Example 139-ExtractConfig() Function

```c
EFI_STATUS
EFIAPI
ExtractConfig (
  IN  CONST EFI_HII_CONFIG_ACCESS_PROTOCOL  *This,
  IN  CONST EFI_STRING                      Request,
  OUT EFI_STRING                            *Progress,
  OUT EFI_STRING                            *Results
  )
{
  EFI_STATUS                                Status;
  UINTN                                     BufferSize;
  DRIVER_SAMPLE_PRIVATE_DATA                *PrivateData;
  EFI_HII_CONFIG_ROUTING_PROTOCOL           *HiiConfigRouting;
  EFI_STRING                                ConfigRequest;
  EFI_STRING                                ConfigRequestHdr;
  UINTN                                     Size;
  BOOLEAN                                   AllocatedRequest;
  
  if (Progress == NULL || Results == NULL) {
    return EFI_INVALID_PARAMETER;
  }
  // Initialize the local variables.
  ConfigRequestHdr = NULL;
  ConfigRequest = NULL;
  Size = 0;
  *Progress = Request;
  AllocatedRequest = FALSE;

  PrivateData = DRIVER_SAMPLE_PRIVATE_FROM_THIS (This);
  HiiConfigRouting = PrivateData->HiiConfigRouting;

  // Get Buffer Storage data from EFI variable.
  // Try to get the current setting from variable.
  BufferSize = sizeof (DRIVER_SAMPLE_CONFIGURATION);
  Status = gRT->GetVariable (
                  VariableName,
                  &mFormSetGuid,
                  NULL,
                  &BufferSize,
                  &PrivateData->Configuration
                  );
  if (EFI_ERROR (Status)) {
    return EFI_NOT_FOUND;
  }
  
  if (Request == NULL) {
    // Request is set to NULL, construct full request string.
    //
    // Allocate and fill a buffer large enough to hold the <ConfigHdr> template
    // followed by "&OFFSET=0&WIDTH=WWWWWWWWWWWWWWWW" followed by a Null-terminator
    ConfigRequestHdr = HiiConstructConfigHdr (
                         &mFormSetGuid,
                         VariableName,
                         PrivateData->DriverHandle[0]
                         );
    Size = (StrLen (ConfigRequestHdr) + 32 + 1) * sizeof (CHAR16);
    ConfigRequest = AllocateZeroPool (Size);
    ASSERT (ConfigRequest != NULL);

    AllocatedRequest = TRUE;
    UnicodeSPrint (
      ConfigRequest,
      Size,
      L"%s&OFFSET=0&WIDTH=%016LX",
      ConfigRequestHdr,
      (UINT64)BufferSize
      );
    FreePool (ConfigRequestHdr);
  } else {
    // Check routing data in <ConfigHdr>.
    // Note: if only one Storage is used, then this checking could be skipped.
    if (!HiiIsConfigHdrMatch (Request, &mFormSetGuid, NULL)) {
      return EFI_NOT_FOUND;
    }
    // Set Request to the unified request string.
    ConfigRequest = Request;
    // Convert buffer data to <ConfigResp> by helper function BlockToConfig()
    Status = HiiConfigRouting->BlockToConfig (
                                 HiiConfigRouting,
                                 ConfigRequest,
                                 (UINT8 *) &PrivateData->Configuration,
                                 BufferSize,
                                 Results, Progress
                                 );
  }
  // Free the allocated config request string.
  if (AllocatedRequest) {
    FreePool (ConfigRequest);

    // Set Progress string to the original request string.
    if (Request == NULL) {
      *Progress = NULL;
    } else if (StrStr (Request, L"OFFSET") == NULL) {
      *Progress = Request + StrLen (Request);
    }
    return Status;
  }

  . .
  return EFI_SUCESS
}
```

###### Example 140-RouteConfig() Function

```c
EFI_STATUS 
EFIAPI
RouteConfig (
  IN  CONST EFI_HII_CONFIG_ACCESS_PROTOCOL  *This,
  IN  CONST EFI_STRING                      Configuration,
  OUT EFI_STRING                            *Progress
  )
{
  EFI_STATUS                                Status;
  UINTN                                     BufferSize;
  DRIVER_SAMPLE_PRIVATE_DATA                *PrivateData;
  EFI_HII_CONFIG_ROUTING_PROTOCOL           *HiiConfigRouting;

  . .

  if (Configuration == NULL || Progress == NULL) {
    return EFI_INVALID_PARAMETER;
  }
  PrivateData = DRIVER_SAMPLE_PRIVATE_FROM_THIS (This);
  HiiConfigRouting = PrivateData->HiiConfigRouting;
  *Progress = Configuration;

  // Check routing data in <ConfigHdr>.
  // Note: if only one Storage is used, then this checking could be
  // skipped. if (!HiiIsConfigHdrMatch (Configuration, &mFormSetGuid, NULL)) { return EFI_NOT_FOUND;
}

//
// Get Buffer Storage data from EFI variable
//
BufferSize = sizeof (DRIVER_SAMPLE_CONFIGURATION);
Status = gRT->GetVariable (
                VariableName,
                &mFormSetGuid,
                NULL,
                &BufferSize,
                &PrivateData->Configuration
                );
if (EFI_ERROR (Status)) {
  return Status;
}
// Convert <ConfigResp> to buffer data by helper function ConfigToBlock() BufferSize = sizeof (DRIVER_SAMPLE_CONFIGURATION);

Status = HiiConfigRouting->ConfigToBlock (
                             HiiConfigRouting,
                             Configuration,
                             (UINT8 *) &PrivateData->Configuration,
                             &BufferSize,
                             Progress
                             );
if (EFI_ERROR (Status)) {
  return Status;
}
// Store Buffer Storage back to EFI variable
Status = gRT->SetVariable (
                VariableName,
                &mFormSetGuid,
                EFI_VARIABLE_NON_VOLATILE | EFI_VARIABLE_BOOTSERVICE_ACCESS,
                sizeof (DRIVER_SAMPLE_CONFIGURATION),
                &PrivateData->Configuration
                );
return Status;
}
```

###### Example 141-Callback function

```c
EFI_STATUS
EFIAPI
DriverCallback (
  IN  CONST EFI_HII_CONFIG_ACCESS_PROTOCOL  *This,
  IN  EFI_BROWSER_ACTION                    Action,
  IN  EFI_QUESTION_ID                       QuestionId,
  IN  UINT8                                 Type,
  IN  EFI_IFR_TYPE_VALUE                    *Value,
  OUT EFI_BROWSER_ACTION_REQUEST            *ActionRequest
  )
{
  DRIVER_SAMPLE_PRIVATE_DATA                *PrivateData;
  EFI_STATUS                                Status;

  if ((Value == NULL) || (ActionRequest == NULL)) {
    return EFI_INVALID_PARAMETER;
  }
  Status = EFI_SUCCESS;
  PrivateData = DRIVER_SAMPLE_PRIVATE_FROM_THIS (This);
  switch (QuestionId) {
  case 0x1234: // do some code break;
  default:
    break;
  }
  return Status;
}
```
