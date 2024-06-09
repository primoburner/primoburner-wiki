---
title: Create a Command Line Tool using CMake on Windows
html_meta:
    description: This page describes the steps needed to configure CMake project for PrimoBurner Command Line Tool on Windows
taxonomy:
    category: docs
---

# Create a Command Line Tool using CMake on Windows

This topic describes the steps needed to configure a CMake project for C++ Command Line Tool. These steps were tested on Windows 11, 23H2. Scripts are `PowerShell`.

## Test that you have all tools installed

CMake:

```powershell
cmake --version
```

ninja:

```powershell
ninja --version
```

If you don't have those tools follow the steps in the [Setup C++ development environment on Windows](https://blog.primosoftware.com/setup-cpp-development-environment-windows/) post to configure a C++ development environment. 

## Create the project directory

```powershell
mkdir ~/primoburner/cmake/enum-devices
```

## Create the CMake project 

Switch to the project directory:

```powershell
cd ~/primoburner/cmake/enum-devices
```

Add `src/main.cpp`:

```cpp
#include <iostream>

int main() {
  std::cout << "Hello PrimoBurner!\n";
}
```

Add `CMakeLists.txt`:

```cmake
cmake_minimum_required(VERSION 3.20)

project(enum-devices)

add_executable(enum-devices src/main.cpp)
```

Add `.gitignore`:

```powershell
.cache/
build/
```

Add `build.ps1`:

```powershell
New-Item -Force -Path ./build/debug -ItemType Directory 
Push-Location ./build/debug
    cmake -G 'Ninja' -DCMAKE_BUILD_TYPE=debug ../..
    ninja
Pop-Location
```

Add `configure.ps1`:

```powershell
$tempFile = [IO.Path]::GetTempFileName()

# get the path to Visual Studio 2022
$vs_install_dir = $(Get-VSSetupInstance | Select-VSSetupInstance -Version '[17.0,18.0]' | Select-Object -ExpandProperty InstallationPath)
$vs_common_tools = "${vs_install_dir}/Common7/Tools/"

# run VsDevCmd.bat for x64 C++ compiler and save the environment to a file
cmd /c " `"$vs_common_tools/VsDevCmd.bat`" -arch=amd64 -host_arch=amd64 && set > `"$tempFile`""

# set the environment into PowerShell
Get-Content $tempFile | Foreach-Object {
    if($_ -match "^(.*?)=(.*)$") {
        Set-Content "env:\$($matches[1])" $matches[2]
    }
}

Remove-Item $tempFile
```

You should end up with the following directory structure:

```sh
enum-devices
├── .gitignore
├── CMakeLists.txt
├── build.ps1
├── configure.ps1
└── src
    └── main.cpp
```

## Test the build

```powershell
# source configure.ps1
. .\configure.ps1

# build
./build.ps1
```

## Update for PrimoBurner

1. [Download](https://github.com/primoburner/primoburner-core/releases/) the 64 bit version of PrimoBurner for C++ (Windows). The file you need will have a name similar to `primoburner-v5.0.1-demo.1-windows.zip` except for the version number which may be different. 

2. Extract the archive in a location of your choice, then copy the `include` and `lib` directories to the `primoburner` subdirectory of the CMake project directory. The CMake project directory is the directory that contains the `CMakeLists.txt` file.

    You should end up with a directory structure similar to the following:

    ```sh
    enum-devices
    ├── .gitignore
    ├── CMakeLists.txt
    ├── primoburner
    │   ├── include
    │   └── lib
    ├── build.ps1
    ├── configure.ps1
    └── src
        └── main.cpp
    ```

3. Replace the contents of `.gitignore` with this code:

    ```
    .cache/
    build/
    primoburner/
    ```

3. Replace the contents of `CMakeLists.txt` with this code:

    ```cpp
    cmake_minimum_required(VERSION 3.16)

    project(enum-devices)
    set (target enum-devices)

    add_executable(${target})

    # debug definitions
    target_compile_definitions(${target} PRIVATE  _DEBUG)

    # debug compile options
    target_compile_options(${target} PRIVATE /Zi /Od)

    # enable C++ 17 standard features
    target_compile_features(${target} PRIVATE cxx_std_17)

    # include dirs
    target_include_directories(${target} PRIVATE primoburner/include)

    # libs
    target_link_directories(${target} PRIVATE primoburner/lib/x64)
    target_link_libraries(${target} PrimoBurner64.dll)

    # add /MTd or /MT compiler option 
    set_property(TARGET ${target} PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")

    # sources
    file(GLOB source "src/*.cpp")
    target_sources(${target} PRIVATE ${source})
    ```

4. Replace the contents of `src/main.cpp` with this code:

    ```cpp
    // Including SDKDDKVer.h defines the highest available Windows platform.

    // If you wish to build your application for a previous Windows platform, include WinSDKVer.h and
    // set the _WIN32_WINNT macro to the platform you wish to support before including SDKDDKVer.h.

    #include <SDKDDKVer.h>

    #include <iostream>

    #include <primo/burner/pb.h>
    #include <primo/platform/reference++.h>

    namespace p = primo;
    namespace pb = primo::burner;

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

10. Build the project

    ```powershell
    . ./configure.ps1
    ./build.ps1
    ```
## Run the application

```powershell
./build/debug/enum-devices
```

You should see a list of all CD / DVD / BD devices that are connected to the system.

## Troubleshooting

* You may get `The program can't start because PrimoBurner64.dll is missing from your computer. Try reinstalling the program to fix this problem.` or a similar message. To fix that, copy the file `PrimoBurner64.dll` from `primoburner/lib/x64` to `build/debug`.
