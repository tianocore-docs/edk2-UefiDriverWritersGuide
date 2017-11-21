<!--- @file
  18.7.1 EfiRom Utility

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

### 18.7.1 EfiRom Utility

The `EfiRom` utility is included with the standard set of tools from the EDK II
project. A pre-built binary of `EfiRom` is in the `BaseTools/Bin/Win32`
directory in the EDK II `WORKSPACE`. This directory, with pre-built binaries,
is automatically added to the path after setting up the EDK II environment, so
`EfiRom` is always available.

The sources to `EfiRom` are in the `BaseTools/Source/C/EfiRom` directory so the
utility can be built for any operating system supporting the EDK II.

Use the `EfiRom` utility to build PCI Option ROM Images containing UEFI
Drivers, PC BIOS legacy option ROM images, or both, in a format conforming to the _PCI 2.3
Specification_ and _PCI 3.0 Specification_. The `EfiRom` utility also allows
UEFI Drivers to be compressed using the UEFI compression algorithm defined in
the Compression Algorithm Specification section of the _UEFI Specification_.

The `EfiRom` utility performs some rudimentary checks on the UEFI Drivers to
verify they are valid PE/COFF images as defined by the _Microsoft Portable
Executable and Common Object File Format Specification_. If any of these checks
fail, the utility aborts without creating the output ROM image file. For
example, the following checks are performed on UEFI Drivers:
* Verification that the DOS stub magic value is `0x5A4D`
* Verification that the PE signature is "`PE\0\0`"
* The `EfiRom` utility also performs rudimentary checking of PC BIOS legacy
  option ROM images. If any of these checks fail, the utility aborts without
  creating the output ROM image file. The following checks are performed on PC
  BIOS legacy option ROMs:
* Verification that the signature of the option ROM header is 0xAA55
* Verification that the offset to the PCI data structure is within the range of
  the file size.
* Verification that the signature of the PCI data structure is "PCIR".

The following example shows the help information from the `EfiRom` utility that is displayed
when the utility is run with no input parameters, the `-h` option or the
`-help` option.

###### Example 197-EfiRom Utility Help

```
 Usage: EfiRom -f VendorId -i DeviceId [options] [file name<s>]
 Copyright (c) 2007 - 2011, Intel Corporation. All rights reserved.

 Options:
 -o FileName, --output FileName
 File will be created to store the output content.
 -e EfiFileName
 EFI PE32 image files.
 -ec EfiFileName
 EFI PE32 image files and will be compressed.
 -b BinFileName
 Legacy binary files.
 -l ClassCode
 Hex ClassCode in the PCI data structure header. -r Rev Hex Revision in the PCI data structure header.
 -n Not to automatically set the LAST bit in the last file.
 -f VendorId
 Hex PCI Vendor ID for the device OpROM, must be specified
 -i DeviceId
 Hex PCI Device ID for the device OpROM, must be specified -p, --pci23
 Default layout meets PCI 3.0 specifications specifying this flag will for a PCI 2.3 layout.
 -d, --dump
 Dump the headers of an existing option ROM image.
 -v, --verbose
 Turn on verbose output with informational messages. --version Show program's version number and exit.
 -h, --help
 Show this help message and exit.
 -q, --quiet
 Disable all messages except FATAL ERRORS.
 --debug [#,0-9]
 Enable debug messages at level #.
```

Examples of generating an Option ROM image using various options provided by
the `EfiRom` utility follow:

`Generate a PCI Option ROM image with a single UEFI binary files.` The output
filename is not specified in command line, so the output filename is
`File2.rom`. The output filename is the same as the first input filename with
the extension `.rom`. When UEFI binary files are specified, the VendorId flag
`-f` and DeviceId flag `-i` must be specified.

**EfiRom -f 0xABCD -i 0x1234 -e File2.efi**

This example shows the output of the `EfiRom` utility then the `-d` option is
used to display the headers from the PCI Option ROM image generated in the
previous example.

###### Example 198-EfiRom Utility Dump Feature

```
Image 1 -- Offset 0x0
ROM header contents
Signature 0xAA55
PCIR offset 0x001C Signature PCIR
Vendor ID 0xABCD
Device ID 0x1234
Length 0x001C
Revision 0x0003 DeviceListOffset 0x00
Class Code 0x000000 Image size 0x1800
Code revision: 0x0000
MaxRuntimeImageLength 0x00
ConfigUtilityCodeHeaderOffset 0x00
DMTFCLPEntryPointOffset 0x00
Indicator 0x80 (last image)
Code type 0x03 (EFI image)
EFI ROM header contents
EFI Signature 0x0EF1
Compression Type 0x0000 (not compressed)
Machine type 0x014C (IA32)
Subsystem 0x000B (EFI boot service driver)
EFI image offset 0x0038 (@0x38)
```

**Generate a PCI Option ROM image with two UEFI binary files and one PC BIOS
legacy option ROM binary file.**

The output filename is not specified in command line so the output filename is
`File1.rom`. The output filename is the same as the first input filename with
the extension `.rom`. When UEFI binary files are specified, the VendorId flag
`-f` and DeviceId flag `-i` must be specified.

**EfiRom -f 0xABCD -i 0x1234 -e File1.efi File2.efi -b Legacy.bin**

`Generate a PCI Option ROM image with two UEFI binary files and one PC BIOS legacy option ROM binary file with the output filename specified on the command line as` `File.rom`

When UEFI binary files are specified, the VendorId flag `-f` and DeviceId flag
`-i` must be specified.

**EfiRom -o File.rom -f 0xABCD -i 0x1234 -e File1.efi File2.efi -b Legacy.bin**

**Generate a PCI Option ROM image with two UEFI binary files and one PC BIOS
legacy option ROM binary file.**

The output filename is specified in command line as `Compressed.rom`. UEFI
binary files are compressed using the UEFI Compression algorithm. When UEFI
binary files are specified, the VendorId flag `-f` and DeviceId flag `-i` must
be specified.

**EfiRom -o Compressed.rom -f 0xABCD -i 0x1234 -ec File1.efi File2.efi -b
Legacy.bin**
