---
title: Create a Command Line Tool using CMake on macOS
metadata:
    description: This page describes the steps needed to configure CMake project for PrimoBurner Command Line Tool on macOS
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
mkdir -p ~/primoburner/cmake/enum-devices
```

## Create the CMake project 

Switch to the project directory:

```bash
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
cmake_minimum_required(VERSION 3.16)

project(enum-devices)

add_executable(enum-devices src/main.cpp)
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
tree -a -L 2 enum-devices

enum-devices
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

## Update for PrimoBurner

1. [Download](https://github.com/primoburner/primoburner-core/releases/) the 64 bit version of PrimoBurner for C++ (macOS). The file you need will have a name similar to `primoburner-v5.0.1-demo.1-darwin.zip` except for the version number which may be different. 

2. Extract the ZIP archive in a location of your choice, then copy the `include` and `lib` directories to the `primoburner` subdirectory of the CMake project directory. The CMake project directory is the directory that contains the `CMakeLists.txt` file.

    You should end up with a directory structure similar to the following:

    ```sh
    enum-devices
    ├── .gitignore
    ├── CMakeLists.txt
    ├── primoburner
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
    primoburner/
    ```

3. Replace the contents of `CMakeLists.txt` with this code:

    ```cpp
    cmake_minimum_required(VERSION 3.16)

    project(enum-devices)
    set (target enum-devices)

    add_executable(${target})

    # definitions for Debug x64
    target_compile_definitions(${target} PUBLIC  _DEBUG)

    # compile options for Debug x64
    target_compile_options(${target} PRIVATE -std=c++17 -stdlib=libc++)
    target_compile_options(${target} PRIVATE -m64 -fPIC)
    target_compile_options(${target} PRIVATE -g)

    # includes
    target_include_directories(${target} PUBLIC primoburner/include)

    # libs
    target_link_directories(${target} PRIVATE ${PROJECT_SOURCE_DIR}/primoburner/lib/x64)
    target_link_libraries(${target} libPrimoBurner.dylib "-framework CoreFoundation" "-framework AppKit")

    # sources
    file(GLOB source "src/*.cpp" "src/*.mm")
    target_sources(${target} PRIVATE ${source})
    ```

4. Replace the contents of `main.cpp` with this code:
		
    ```cpp
    //
    //  main.cpp
    //  enum-devices
    //
    
    #include <iostream>
    
    #include <primo/burner/pb.h>
    #include <primo/platform/ustring.h>
    #include <primo/platform/reference++.h>
    
    namespace p = primo;
    namespace pb = primo::burner;
    
    int main(int argc, const char * argv[]) {
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
    
                auto description = p::ustring(device->description());
    
                cout << "Device       : " << i << endl;
                cout << "Description  : " << description.str() << endl;
    
                cout << endl;
            }
        }
    
        // terminate engine
        engine->shutdown();
        
        return 0;
    }
    ```
    
10. Build the project

    ```bash
    ./build.sh
    ```

## Run the application

```bash
./build/debug/enum-devices
```

You should see a list of all CD / DVD / BD devices that are connected to the system.      
