<!--- @file
  Tables

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

### Tables

* [Table 1-Organization of the UEFI Driver Writers Guide](1_introduction/12_organization_of_this_document.md#table-1-organization-of-the-uefi-driver-writers-guide)
* [Table 2-Classes of UEFI drivers to develop](2_uefi_driver_implementation_checklist/21_design_and_implementation_of_uefi_drivers.md#table-2-classes-of-uefi-drivers-to-develop)
* [Table 3-Protocols produced by various devices](2_uefi_driver_implementation_checklist/21_design_and_implementation_of_uefi_drivers.md#table-3-protocols-produced-by-various-devices)
* [Table 4-Mapping operations to UEFI drivers](2_uefi_driver_implementation_checklist/22_how_to_implement_features_in_edk_ii.md#table-4-mapping-operations-to-uefi-drivers)
* [Table 5-Description of handle types](3_foundation/34_handle_database.md#table-5-description-of-handle-types)
* [Table 6-Description of image types](3_foundation/37_uefi_images/README.7.md#table-6-description-of-image-types)
* [Table 7-Description of event types](3_foundation/38_events_and_task_priority_levels/README.8.md#table-7-description-of-event-types)
* [Table 8-Task priority levels defined in UEFI](3_foundation/38_events_and_task_priority_levels/README.8.md#table-8-task-priority-levels-defined-in-uefi)
* [Table 9-Types of device path nodes defined in UEFI Specification](3_foundation/39_uefi_device_paths/README.9.md#table-9-types-of-device-path-nodes-defined-in-uefi-specification)
* [Table 10-Protocols separating the loading and starting/stopping of drivers](3_foundation/310_uefi_driver_model/README.10.md#table-10-protocols-separating-the-loading-and-startingstopping-of-drivers)
* [Table 11-I/O protocols produced in the Start() function for different device classes](3_foundation/310_uefi_driver_model/3101_device_driver.md#table-11-io-protocols-produced-in-the-start-function-for-different-device-classes)
* [Table 12-Connecting controllers: Driver connection precedence rules](3_foundation/314_uefi_driver_model_connection_process/3141_connectcontroller.md#table-12-connecting-controllers-driver-connection-precedence-rules)
* [Table 13-UEFI console drivers](3_foundation/315_platform_initialization/3154_console_drivers.md#table-13-uefi-console-drivers)
* [Table 14-Alternate key sequences for remote terminals](4_general_driver_design_guidelines/42_maximize_platform_compatibility/4218_offer_alternatives_to_function_keys.md#table-14-alternate-key-sequences-for-remote-terminals)
* [Table 15-Space optimizations](4_general_driver_design_guidelines/44_optimization_techniques/441_size_reduction.md#table-15-space-optimizations)
* [Table 16-Speed optimizations](4_general_driver_design_guidelines/44_optimization_techniques/442_performance_optimizations.md#table-16-speed-optimizations)
* [Table 17-Alphabetical listing of UEFI services](5_uefi_services/README.md#table-17-alphabetical-listing-of-uefi-services)
* [Table 18-UEFI services that are commonly used by UEFI drivers](5_uefi_services/51_services_that_uefi_drivers_commonly_use/README.md#table-18-uefi-services-that-are-commonly-used-by-uefi-drivers)
* [Table 19-UEFI services that are rarely used by UEFI drivers](5_uefi_services/52_services_that_uefi_drivers_rarely_use/README.2.md#table-19-uefi-services-that-are-rarely-used-by-uefi-drivers)
* [Table 20-UEFI services that should not be used by UEFI drivers](5_uefi_services/53_services_that_uefi_drivers_should_not_use/README.3.md#table-20-uefi-services-that-should-not-be-used-by-uefi-drivers)
* [Table 21-UEFI Driver Feature Selection Matrix](7_driver_entry_point/71_optional_features.md#table-21-uefi-driver-feature-selection-matrix)
* [Table 22-Service Binding Protocols](10_uefi_service_binding_protocol/README.md#table-22-service-binding-protocols)
* [Table 23-Health Status Values](14_driver_health_protocol/142_gethealthstatus_implementations/README.md#table-23-health-status-values)
* [Table 24-UEFI Specific Revision Values](16_driver_supported_efi_version_protocol/161_driver_supported_efi_version_protocol_implemen.md#table-24-uefi-specific-revision-values)
* [Table 25-Classes of PCI drivers](18_pci_driver_design_guidelines/README.md#table-25-classes-of-pci-drivers)
* [Table 26-PCI Attributes](18_pci_driver_design_guidelines/183_pci_drivers/1832_start_and_stop.md#table-26-pci-attributes)
* [Table 27-EDK II attributes #defines](18_pci_driver_design_guidelines/183_pci_drivers/1832_start_and_stop.md#table-27-edk-ii-attributes-defines)
* [Table 28-PCI BAR attributes](18_pci_driver_design_guidelines/183_pci_drivers/1832_start_and_stop.md#table-28-pci-bar-attributes)
* [Table 29-PCI Embedded Device Attributes](18_pci_driver_design_guidelines/183_pci_drivers/1832_start_and_stop.md#table-29-pci-embedded-device-attributes)
* [Table 30-Classes of USB drivers](19_usb_driver_design_guidelines/README.md#table-30-classes-of-usb-drivers)
* [Table 31-Classes of SCSI drivers](20_scsi_driver_design_guidelines/README.md#table-31-classes-of-scsi-drivers)
* [Table 32-SCSI device path examples](20_scsi_driver_design_guidelines/201_scsi_host_controller_driver/2018_scsi_device_path.md#table-32-scsi-device-path-examples)
* [Table 33-Classes of ATA drivers](21_ata_driver_design_guidelines/README.md#table-33-classes-of-ata-drivers)
* [Table 34-SATA device path examples](21_ata_driver_design_guidelines/211_ata_host_controller_driver/2114_ata_device_paths.md#table-34-sata-device-path-examples)
* [Table 35-Serial I/O protocol control bits](22_text_console_driver_design_guidelines/224_serial_io_protocol_implementations/2243_setcontrol_and_getcontrol_implementation.md#table-35-serial-io-protocol-control-bits)
* [Table 36-Network driver differences](25_network_driver_design_guidelines/README.md#table-36-network-driver-differences)
* [Table 37-!PXE interface structure](25_network_driver_design_guidelines/252_nii_protocol_and_undi_implementations/README.md#table-37-!pxe-interface-structure)
* [Table 38-CDB structure](25_network_driver_design_guidelines/252_nii_protocol_and_undi_implementations/README.md#table-38-cdb-structure)
* [Table 39-Reserved directory names](30_building_uefi_drivers/303_create_uefi_driver_directory/3032_reserved_directory_names.md#table-39-reserved-directory-names)
* [Table 40-UEFI Shell commands](31_testing_and_debugging_uefi_drivers/313_uefi_shell_debugging/3131_testing_specific_protocols.md#table-40-uefi-shell-commands)
* [Table 41-Other Shell Testing Procedures](31_testing_and_debugging_uefi_drivers/313_uefi_shell_debugging/3132_other_testing.md#table-41-other-shell-testing-procedures)
* [Table 42-UEFI Shell commands for loading UEFI drivers](31_testing_and_debugging_uefi_drivers/313_uefi_shell_debugging/3133_loading_uefi_drivers.md#table-42-uefi-shell-commands-for-loading-uefi-drivers)
* [Table 43-UEFI Shell commands for unloading UEFI drivers](31_testing_and_debugging_uefi_drivers/313_uefi_shell_debugging/3134_unloading_uefi_drivers.md#table-43-uefi-shell-commands-for-unloading-uefi-drivers)
* [Table 44-UEFI Shell commands for connecting UEFI drivers](31_testing_and_debugging_uefi_drivers/313_uefi_shell_debugging/3135_connecting_uefi_drivers.md#table-44-uefi-shell-commands-for-connecting-uefi-drivers)
* [Table 45-UEFI Shell commands for driver and device information](31_testing_and_debugging_uefi_drivers/313_uefi_shell_debugging/3136_driver_and_device_information.md#table-45-uefi-shell-commands-for-driver-and-device-information)
* [Table 46-Error levels](31_testing_and_debugging_uefi_drivers/314_debugging_code_statements/README.4.md#table-46-error-levels)
* [Table 47-UEFI Driver Properties](appendix_b_edk_ii_sample_drivers.md#table-47-uefi-driver-properties)
* [Table 48-Sample UEFI Driver Properties](appendix_b_edk_ii_sample_drivers.md#table-48-sample-uefi-driver-properties)
* [Table 49-Definitions of terms](appendix_c_glossary.md#table-49-definitions-of-terms)
