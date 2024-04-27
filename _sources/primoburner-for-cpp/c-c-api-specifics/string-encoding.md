---
title: String Encoding
metadata:
    description: This topic describes the string encoding used by the PrimoBurner C and C++ APIs.
taxonomy:
    category: docs
---

# String Encoding

This topic describes the string encoding used by the PrimoBurner C++ API.

## Unicode Strings

The PrimoBurner API uses UTF-16 unicode encoding.

The PrimoBurner API character type is `char_t`, where `char_t` represents a UTF-16 character and is defined as a 16 bit unsigned integer on all platforms (Windows, Mac and Linux).

Note: A Unicode character can be expressed by 1 or 2 UTF-16 code units, i.e. by 2 or 4 bytes.

### Windows

On Windows the `char_t` type is the same as the `wchar_t` type. Your Windows application must be compiled for Unicode OR alternatively you should convert strings to `wchar_t` before passing calling the PrimoBurner API.

### Mac

#### CoreFoundation

`CFStringRef` is UTF-16, so the encoding matches the `char_t` encoding. In your code, you can use the `CFStringGetCharactersPtr` function:

``` cpp
// Get a UniChar* pointer from CFStringRef and pass it to PrimoBurner

NSString theString;
const UniChar* theStringPtr = CFStringGetCharactersPtr((__bridge CFStringRef) theString);

primo::burner::DataFile* dataFile = primo::burner::Library::createDataFile();
    dataFile->setLongFilename(theStringPtr);
dataFile->release();
```

#### Foundation

NSString is UTF-16. The UTF-16 encoding matches the PrimoBurner API. You can use the `CFStringGetCharactersPtr` function:

``` cpp
// Get a UniChar* pointer from NSString and pass it to PrimoBurner

NSString theString;
const UniChar* theStringPtr = CFStringGetCharactersPtr((__bridge CFStringRef) theString);

primo::burner::DataFile* dataFile = primo::burner::Library::createDataFile();
    dataFile->setLongFilename(theStringPtr);
dataFile->release();
```

#### Command Line

On Mac / Unix, `wchar_t` is defined as a 32-bit unsigned integer by default. Conversion is required between your code (if using UTF-8 or UTF-32 encoding)  and `char_t` (UTF-16 encoding). 

PrimoBurner comes with the `primo::ustring` helper class (defined in `include/primo/ustring.h`) which can be used for string conversion:
 
``` cpp

```

### Linux

#### Command Line

On Linux, `wchar_t` is defined as a 32-bit unsigned integer by default. Conversion is required between your code (if using UTF-8 or UTF-32 encoding) and `char_t` (UTF-16 encoding). 

PrimoBurner comes with the `primo::ustring` helper class (defined in `include/PrimoUString.h`) which can be used for string conversion. The `MetaInfo` sample demonstrates the use of the `primo::ustring` class.

For example, this line uses the `ustring` class:

```cpp
auto description = p::ustring(device->description());
```

from the [Getting Started on Linux](/primoburner-for-cpp/getting-started-linux/create-cpp-command-line-tool-cmake) example:

``` cpp
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

## ANSI Strings

A few functions in the PrimoBurner API may use plain 8-bit ANSI `char` for convenience. For example, the `Library::setLicense` takes an ANSI string as input:

``` cpp
Library::setLicense("license-xml-string");
```
