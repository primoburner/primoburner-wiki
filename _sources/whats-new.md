---
title: What’s New
metadata:
    description: Discover what&#039;s new and improved in the latest PrimoBurner version.
taxonomy:
    category: docs
toc:
    baselevel: 1                 # Base level for headings
    headinglevel: 2              # Maximum heading level to show in TOC    
---

# What’s New

## Version 5.0

> 11-April-2024

### API

* Support ARM 64-bit architecture: Windows arm64, macOS arm64, and Ubuntu aarch64 
* Remove 32-bit application support (C++ and .NET)
* C\+\+: Refactor API headers to use lowercase file names and subdirectories for namespaces
* .NET: New PrimoBurner .NET SDK for macOS and Linux (based on .NET Core 6+)
* Distribute the PrimoBurner Core and PrimoBurner .NET Core through GitHub

### Samples 

* Use cmake and Visual Studio Code for PrimoBurner samples
* GitHub repositories for PrimoBurner samples
* C\+\+: New [bluray-data](https://github.com/primoburner/primoburner-cpp/tree/main/samples/windows/bluray-data) CLI sample

### Docs

* Split the wiki to separate C\+\+ and .NET sites

---

## Version 4.6.1

> 14-March-2016

- Updated bundled AVBlocks to version 1.26

## Version 4.6

> 22-January-2016

- Updated bundled AVBlocks to version 1.25
- Moved .NET projects to CLR 4, i.e. .NET 4.0, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1

## Version 4.5

> 29-June-2015

### Device

- Write + Verify recording mode for DVD-RAM and BD-RE media. You can turn the write+verify recording by setting the [Device.DVDWriteVerify](http://doc.primoburner.com/net/latest/class_primo_software_1_1_burner_1_1_device.html#abb26ee446287fd69049e0ff4aa96b4a3) or [Device.BDWriteVerify](http://doc.primoburner.com/net/latest/class_primo_software_1_1_burner_1_1_device.html#acd7b4eb9b01d3b4e7295db396736c890) properties.
