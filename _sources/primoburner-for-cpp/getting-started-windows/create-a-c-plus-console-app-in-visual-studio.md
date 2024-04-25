---
title: Create a C++ Console App in Visual Studio on Windows
metadata:
    description: This topic describes the steps needed to configure a C++ console application in Visual Studio.
taxonomy:
    category: docs
---

# Create a Console App in Visual Studio on Windows

This topic describes the steps needed to configure a C++ Console App in Visual Studio. These steps have been verified to work with Visual Studio 2022 Community Edition, on Windows 11 (22H2).

## Create the Visual Studio project

1. Create a C++, Console App in Visual Studio. Name the project `enum-devices`. Check `Place solution and project in the same directory`. 

2. [Download](https://github.com/primoburner/primoburner-core/releases/) the 64 bit version of PrimoBurner for C++ (Windows). The file you need will have a name similar to `primoburner-v5.0.1-demo.1-windows.zip` - the version number may differ. 

3. Extract the ZIP archive in a location of your choice, then copy the `include` and `lib` directories to the `primoburner` subdirectory of the Visual Studio solution directory. The Visual Studio solution is the directory that contains the `enum-devices.sln` solution file.
    
    You should end up with a directory structure similar to the following:

    ```
    enum-devices
    ├───primoburner
    │   ├───include
    │   └───lib
    ├───enum-devices.cpp
    ├───enum-devices.sln
    └───enum-devices.vcxproj
    ```

4. Replace the contents of `enum-devices.cpp` with this code:

    ```cpp
    // enum-devices.cpp : This file contains the 'main' function. Program execution begins and ends there.
    //

    // Including SDKDDKVer.h defines the highest available Windows platform.

    // If you wish to build your application for a previous Windows platform, include WinSDKVer.h and
    // set the _WIN32_WINNT macro to the platform you wish to support before including SDKDDKVer.h.

    #include <SDKDDKVer.h>

    #include <iostream>

    #include <primo/burner/pb.h>
    #include <primo/platform/reference++.h>

    namespace p = primo;
    namespace pb = primo::burner;

    // link with PrimoBurner64.lib
    #pragma comment(lib, "./primoburner/lib/x64/PrimoBurner64.lib")

    int main()
    {
        // Create engine
        auto engine = p::make_ref(pb::Library::createEngine());

        // Initialize engine
        engine->initialize();

        // create device enumerator
        auto enumerator = p::make_ref(engine->createDeviceEnumerator());

        for (int i = 0; i < enumerator->count(); i++) {
            // create a device; do not ask for exclusive access
            auto device = p::make_ref(enumerator->createDevice(i, false));
            
            if (device) {
                using namespace std;

                wcout << "Device       : " << i  << endl;
                wcout << "Description  : " << device->description() << endl;

                wcout << endl;
            }
        }

        // terminate engine
        engine->shutdown();

        return 0;
    }
    ```

5. In Visual Studio, select `Build | Configuration Manager` from the menu, then select the `x64` platform to the solution platforms.

6. In Visual Studio, select `Project | enum-devices Properties` from the menu, then `C++ | General`, then add `./primoburner/include` to `Additional Include Directories`.

7. Build the project (Ctrl + Shift + B).

8. Copy the file `PrimoBurner64.dll` from `primoburner/lib/x64` to `x64/Debug`. 

9. Run the application in Visual Studio. You should see a list of all CD / DVD / BD devices that are connected to the system.

## Troubleshooting

* You may get `The program can't start because PrimoBurner64.dll is missing from your computer. Try reinstalling the program to fix this problem.` or a similar message. To fix that, copy the file `PrimoBurner64.dll` from `primoburner/lib/x64` to `x64/Debug`.
