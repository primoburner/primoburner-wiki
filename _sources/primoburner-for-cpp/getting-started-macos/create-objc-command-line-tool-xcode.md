---
title: Create an Objective-C Command Line Tool in Xcode on macOS
metadata:
    description: This page describes the steps needed to configure an Objective-C Command Line Tool in Xcode.
taxonomy:
    category: docs
---

# Create a Command Line Tool in Xcode on macOS

This topic describes the steps needed to configure an Objective-C Command Line Tool in Xcode. These steps have been verified to work with Xcode 15.0.1, on macOS Ventura 13.5.2.

## Create the Xcode project 

1. Open Xcode, select `Create New Project`, pick `macOS` as a platform, pick `Command Line Tool` for Application 

2. Set Product Name to `enum-devices`, select `Objective-C` for language.

3. Rename `main.m` to `main.mm`. 

4. [Download](https://github.com/primoburner/primoburner-core/releases/) the 64 bit version of PrimoBurner for C++ (macOS). The file you need will have a name similar to `primoburner_v5.0.1-demo.1-darwin.zip` except for the version number which may be different. 

5. Extract the ZIP archive in a location of your choice, then copy the `include` and `lib` directories to the `primoburner` subdirectory of the Xcode project directory. The Xcode project directory is the directory that contains the `enum-devices.xcodeproj` project file.

    You should end up with a directory structure similar to the following:

    ```sh
    enum-devices
    ├── primoburner
    │   ├── include
    │   └── lib
    ├── enum-devices
    │   └── main.mm
    └── enum-devices.xcodeproj
    ```

6. In Xcode, select the `enum-devices` project in Xcode, and then the 'Build Settings' tab: 
    * Under Apple Clang - Language - C++, set the C++ Language Dialect to `C++17[-std=c++17]`
    * Under Search Paths | Header Search Paths, add the `$(PROJECT_DIR)/primoburner/include` directory to the list
    * Under linking - General | Runpath Search Paths, add `@executable_path` to the list 
    * Set the Build Products Path to `$(PROJECT_DIR)/build`

7. In Xcode, select the 'enum-devices' target, and then the 'Build Phases' tab:
	* Expand the 'Link Binary with Libraries' section 
	* Add the `libPrimoBurner.dylib` from the `$(PROJECT_DIR)/primoburner/lib/x64` directory.

8. Replace the contents of `main.mm` with this code:
		
    ```objectivec
    //
    //  main.mm
    //  enum-devices
    //
    
    #import <Foundation/Foundation.h>
    
    #pragma clang diagnostic push
    #pragma clang diagnostic ignored "-Weverything"
    
    #include <iostream>
    
    #include <primo/burner/pb.h>
    #include <primo/platform/ustring.h>
    #include <primo/platform/reference++.h>
    
    #pragma clang diagnostic pop
    
    namespace p = primo;
    namespace pb = primo::burner;
    
    int main(int argc, const char * argv[]) {
        @autoreleasepool {
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
        }
        
        return 0;
    }
    ```

9. Restart Xcode!!! Otherwise it will not pick the new Build Products Path 

10. Build the project ( ⌘B )  

11. Copy the file `libPrimoBurner.dylib` from `primoburner/lib/x64` to `build/Debug`. 

## Troubleshooting

* You may get `dyld: Library not loaded: @executable_path/libPrimoBurner.dylib` or a similar message. To fix that, copy the file `libPrimoBurner.dylib` from `primoburner/lib` to `build/Debug`.
