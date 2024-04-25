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

1. Create a C++, Console App in Visual Studio. Name the project `simple-converter`. Check `Place solution and project in the same directory`. 

2. [Download](https://github.com/avblocks/avblocks-core/releases/) the 64 bit version of AVBlocks for C++ (Windows). The file you need will have a name similar to `avblocks_v3.0.0-demo.1-windows.zip` - the version number may differ. 

3. Extract the ZIP archive in a location of your choice, then copy the `include` and `lib` directories to the `avblocks` subdirectory of the Visual Studio solution directory. The Visual Studio solution is the directory that contains the `simple-converter.sln` solution file.
    
    You should end up with a directory structure similar to the following:

    ```
    simple-converter
    ├───avblocks
    │   ├───include
    │   └───lib
    ├───simple-converter.cpp
    ├───simple-converter.sln
    └───simple-converter.vcxproj
    ```

4. Replace the contents of `simple-converter.cpp` with this code:

    ```cpp
    #include <primo/avblocks/avb.h>
    #include <primo/platform/reference++.h>

    // link with AVBlocks64.lib
    #pragma comment(lib, "./avblocks/lib/x64/AVBlocks64.lib")

    using namespace primo::codecs;
    using namespace primo::avblocks;

    int main(int argc, const char * argv[]) {
        // needed for WMV
        CoInitializeEx(nullptr, COINITBASE_MULTITHREADED);

        Library::initialize();

        auto inputInfo = primo::make_ref(Library::createMediaInfo());
        inputInfo->inputs()->at(0)->setFile(L"Wildlife.wmv");

        if (inputInfo->open()) {
            auto inputSocket = primo::make_ref(Library::createMediaSocket(inputInfo.get()));
            auto outputSocket = primo::make_ref(Library::createMediaSocket(Preset::Video::Generic::MP4::Base_H264_AAC));
            outputSocket->setFile(L"Wildlife.mp4");

            auto transcoder = primo::make_ref(Library::createTranscoder());
            transcoder->inputs()->add(inputSocket.get());
            transcoder->outputs()->add(outputSocket.get());

            if (transcoder->open()) {
                transcoder->run();
                transcoder->close();
            }
        }

        Library::shutdown();

        CoUninitialize();

        return 0;
    }
    ```

5. In Visual Studio, select `Build | Configuration Manager` from the menu, then select the `x64` platform to the solution platforms.

6. In Visual Studio, select `Project | simple-converter Properties` from the menu, then `C++ | General`, then add `./avblocks/include` to `Additional Include Directories`.

7. Build the project (Ctrl + Shift + B).

8. Copy the file `AVBlocks64.dll` from `avblocks/lib/x64` to `x64/Debug`. 

## Run the application

1. Download the `Wildlife.wmv` HD movie from the [Internet Archive](https://archive.org/download/WildlifeHd/Wildlife.wmv) and save it in the Visual Studio solution directory (next to the  `simple-converter.sln` solution file).

2. Run the application in Visual Studio. Wait a few seconds for the Transcoder to finish. The converted file `Wildlife.mp4` will be in the solution directory.   

## Troubleshooting

* You may get `The program can't start because AVBlocks64.dll is missing from your computer. Try reinstalling the program to fix this problem.` or a similar message. To fix that, copy the file `AVBlocks64.dll` from `avblocks/lib/x64` to `x64/Debug`.

* `transcoder->open()` may fail if there is already a file `Wildlife.mp4` in the project directory. Delete `Wildlife.mp4` to solve that.   
