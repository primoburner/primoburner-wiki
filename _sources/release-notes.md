---
title: Release Notes
metadata:
    description: This topic describes the changes in each version of PrimoBurner.
taxonomy:
    category: docs
toc:
    baselevel: 1                 # Base level for headings
    headinglevel: 2              # Maximum heading level to show in TOC    
---

# Release Notes

## Version 5.0

### New

- [PB-273] - Build for macOS arm64
- [PB-274] - Build for Ubuntu arm64
- [PB-291] - Build .NET version for ARM \(macOS\)
- [PB-292] - Build .NET version for ARM \(Ubuntu\)
- [PB-298] - C\+\+: bluray-data CLI sample  - \(Windows\)
- [PB-299] - C\+\+: bluray-data CLI sample  - \(Linux\)
- [PB-300] - C\+\+: bluray-data CLI sample  - \(macOS\)
- [PB-318] - Samples: Publish C\+\+ CLI samples to GitHub
- [PB-320] - C\+\+: Publish PrimoBurner Core Demo v5.0.1 to GitHub 
- [PB-321] - .NET: Publish PrimoBurner .NET Core Demo v5.0.1 to GitHub

### Fix

- [PB-302] - ScsiInquiry takes a long time on Raspbery Pi 4 Ubuntu aarch64
- [PB-309] - DataDisc: Wrong creation datetime for files and directories on Linux / macOS
- [PB-313] - DataDisc: Wrong creation datetime for files and directories on Windows
- [PB-323] - Docs: C\+\+: Fix DeviceEnum::createDevice documentation
- [PB-277] - Show "Unregistered version / Demo version" message for DEMO builds 

---

## Version 4.7.8

### Update

* [PB-246] - Update AVBlocks to 2.3.4

---

## Version 4.7.4

### Update

* [PB-245] - Update AVBlocks to 2.3.3

---

## Version 4.7.3

### Update

* [PB-243] - Update AVBlocks to 2.3.2

---

## Version 4.7.2

### Update

* [PB-241] - Update AVBlocks to 2.3.1

---

## Version 4.7.1

### Update

- [PB-240] – Update AVBlocks to 2.2.1

### Fix

- [PS-40] – Mac: Crash when enumerating devices in which there are discs with volume names shorter than 10 characters

- - - - - -

## Version 4.7

### New

- [PB-230] – Samples: New ‘enum\_devices` sample
- [PB-227] – Library: Windows: Support devices that do not have drive letters assigned.
- [PB-224] – DeviceEnum: Windows: Do not require drive letters during device enumeration

### Update

- [PB-236] – Update AVBlocks to 2.2
- [PB-232] – Mac: Provide 32-Bit + 64-Bit universal lib for Mac
- [PB-237] – Linux: Use Qt5 in GUI samples

- - - - - -

## Version 4.6.1

### Update

- [PB-225] – Update AVBlocks to 1.26

- - - - - -

## Version 4.6

### Update

- [PB-220] – Samples: Windows: Upgrade sample projects to Visual Studio 2015
- [PB-221] – Samples: Windows: Deprecate the CLR2 .NET projects
- [PB-222] – Update AVBlocks to 1.25

- - - - - -

## Version 4.5

### New

- [PB-216] – Device: Write+Verify recording mode for DVD-RAM and BD-RE media

### Update

- [PB-217] – Device: Rename `Device.BDRWriteVerification` to `Device.BDWriteVerify`
- [PB-218] – Update AVBlocks to version 1.19

### Fix

- [PB-215] – System.EntryPointNotFoundException: Unable to find an entry point named ‘TocTrack\_adr’ in DLL

- - - - - -

## Version 4.4.2

### Update

- [PB-204] – Samples: Mac: Upgrade C++ samples to Qt 5.x
- [PB-210] – Update AVBlocks to 1.18

### Fix

- [PB-203] – Mac: Pure virtual function called exception in ReadCDSession sample
- [PB-209] – Device: Device.WriteTransferRate returns zero for BD-RE media

- - - - - -

## Version 4.4.1

### New

- [PB-188] – Samples: .NET: New MultiAudioCD sample

### Update

- [PB-185] – Docs: .NET: Fix the code samples in the DiscLayout and DiscArchive docs
- [PB-186] – Docs: C++: Fix the code sample in the DiscArchive docs
- [PB-191] – Update AVBlocks to version 1.16

- - - - - -

## Version 4.4

### Update

- [PB-97] – Docs: C++: Add a page that describes the API changes for PrimoBurner 4

### Fix

- [PB-179] – .NET: CachePolicy.CacheSmallFiles causes entry point exception
- [PB-182] – .NET: ErrorFacility enum has the wrong values for Mac and POSIX subsytems
- [PB-183] – C++: AVBlocks libraries are missing from the C++ distribution

- - - - - -

## Version 4.3

### Update

- [PB-141] – Samples: C++: BluRayBurner: Add Close Track and Close Session options
- [PB-142] – Samples: C++: BluRayBurner: Choose format type and subtype before formatting.

### Fix

- [PB-131] – Write errors on PIONEER , BD-RW BDR-206D v9.56
- [PB-140] – Wrong seconds in B0 pointer when writing CD using WriteMethod::RawDao2352.
- [PB-143] – Device::isMediaFormatted reports false for formatted BD-R with spare areas (BD-R SRM-POW)
- [PB-144] – Last written DVD ECC block and BD Cluster is padded with zeros. This affects random disc writes.
- [PB-145] – Partially formatted DVD-RW media is reported as blank on some LITE-ON devices.
