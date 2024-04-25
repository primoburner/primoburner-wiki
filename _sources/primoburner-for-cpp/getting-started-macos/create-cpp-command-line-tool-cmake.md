---
title: Create a Command Line Tool using CMake on macOS
metadata:
    description: This page describes the steps needed to configure CMake project for AVBlocks Command Line Tool on macOS
taxonomy:
    category: docs
---

# Create a Command Line Tool using CMake on macOS

This topic describes the steps needed to configure a CMake project for C++ Command Line Tool. These steps have been verified to work with Xcode 15.0.1, on macOS Ventura 13.5.2.

## Test that you have all tools installed

C++ Compiler (clang):

```sh
clang --version
```

CMake:

```sh
cmake --version
```

ninja:

```sh
ninja --version
```

If you don't have those tools follow the steps in the [Setup C++ development environment on macOS](https://blog.primosoftware.com/setup-cpp-development-environment-macos/) post to configure a C++ development environment. 

## Create the project directory

```bash
mkdir -p ~/avblocks/cmake/simple-converter
```

## Create the CMake project 

Switch to the project directory:

```bash
cd ~/avblocks/cmake/simple-converter
```

Add `src/main.cpp`:

```cpp
#include <iostream>

int main() {
  std::cout << "Hello AVBlocks!\n";
}
```

Add `CMakeLists.txt`:

```cmake
cmake_minimum_required(VERSION 3.16)

project(simple-converter)

add_executable(simple-converter src/main.cpp)
```

Add `build.sh`:

```bash
#!/usr/bin/env bash

mkdir -p ./build/debug
pushd ./build/debug
    cmake -G 'Ninja' -DCMAKE_BUILD_TYPE=Debug  ../.. && \
    ninja
    ret=$?
popd  
```

Add `.gitignore`:

```bash
.DS_Store
.cache/
build/
```

You should end up with the following directory structure:

```sh
tree -a -L 2 simple-converter

simple-converter
├── .gitignore
├── CMakeLists.txt
├── build.sh
└── src
    └── main.cpp
```

## Test the build

```bash
chmod +x build.sh

./build.sh
```

## Update for AVBlocks

1. [Download](https://github.com/avblocks/avblocks-core/releases/) the 64 bit version of AVBlocks for C++ (macOS). The file you need will have a name similar to `avblocks_v3.0.0-demo.1-darwin.zip` except for the version number which may be different. 

2. Extract the ZIP archive in a location of your choice, then copy the `include` and `lib` directories to the `avblocks` subdirectory of the CMake project directory. The CMake project directory is the directory that contains the `CMakeLists.txt` file.

    You should end up with a directory structure similar to the following:

    ```sh
    simple-converter
    ├── .gitignore
    ├── CMakeLists.txt
    ├── avblocks
    │   ├── include
    │   └── lib
    ├── build.sh
    └── src
        └── main.cpp
    ```

3. Replace the contents of `.gitignore` with this code:

    ```
    .DS_Store
    .cache/
    build/
    avblocks/
    ```

3. Replace the contents of `CMakeLists.txt` with this code:

    ```cpp
    cmake_minimum_required(VERSION 3.16)

    project(simple-converter)
    set (target simple-converter)

    add_executable(${target})

    # definitions for Debug x64
    target_compile_definitions(${target} PUBLIC  _DEBUG)

    # compile options for Debug x64
    target_compile_options(${target} PRIVATE -std=c++17 -stdlib=libc++)
    target_compile_options(${target} PRIVATE -m64 -fPIC)
    target_compile_options(${target} PRIVATE -g)

    # includes
    target_include_directories(${target} PUBLIC avblocks/include)

    # libs
    target_link_directories(${target} PRIVATE ${PROJECT_SOURCE_DIR}/avblocks/lib/x64)
    target_link_libraries(${target} libAVBlocks.dylib "-framework CoreFoundation" "-framework AppKit")

    # sources
    file(GLOB source "src/*.cpp" "src/*.mm")
    target_sources(${target} PRIVATE ${source})
    ```

4. Replace the contents of `main.cpp` with this code:
		
    ```cpp
    //
    //  main.cpp
    //  simple-converter
    //

    #include <primo/avblocks/avb.h>
    #include <primo/platform/reference++.h>
    #include <primo/platform/ustring.h>

    using namespace primo;
    using namespace primo::codecs;
    using namespace primo::avblocks;

    int main(int argc, const char *argv[]) {
        Library::initialize();

        auto inputFile = primo::ustring(L"AAP.m4v");
        auto outputFile = primo::ustring(L"AAP.mp4");

        auto inputInfo = primo::make_ref(Library::createMediaInfo());
        inputInfo->inputs()->at(0)->setFile(inputFile.u16());

        if (inputInfo->open()) {
            auto inputSocket = primo::make_ref(Library::createMediaSocket(inputInfo.get()));
            auto outputSocket = primo::make_ref(Library::createMediaSocket(Preset::Video::Generic::MP4::Base_H264_AAC));
            outputSocket->setFile(outputFile.u16());

            auto transcoder = primo::make_ref(Library::createTranscoder());
            transcoder->inputs()->add(inputSocket.get());
            transcoder->outputs()->add(outputSocket.get());

            if (transcoder->open()) {
                transcoder->run();
                transcoder->close();
            }
        }

        Library::shutdown();
        return 0;
    }
    ```

10. Build the project

    ```bash
    ./build.sh
    ```
## Run the application

1. Download the `AAP.m4v` HD movie from the [Internet Archive](https://archive.org/details/Wildlife-filming) and save it in the project directory.

2. Run the application:

    ```bash
    ./build/debug/simple-converter
    ```
    
    Wait for the Transcoder to finish - it will take a few minutes. The converted file `AAP.mp4` will be in the project directory.
	
## Troubleshooting

* `transcoder->open()` may fail if there is already a file `AAP.mp4` in the project directory. Delete `AAP.mp4` to solve that.         
