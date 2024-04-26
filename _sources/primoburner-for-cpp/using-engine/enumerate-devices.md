---
title: Enumerate Devices
metadata:
    description: How to enumerate the optical devices connected to your system.
taxonomy:
    category: [docs]
    tags: [Device,DeviceEnum,Engine]
---

# Enumerate Devices

Enumerate the optical devices connected to your system.

To enumerate the available devices:

 1. Obtain a {DeviceEnum}` ` object by calling the {Engine_createDeviceEnumerator}` ` method.  

 2. Use the `DeviceEnum::createDevice` method to create a {Device}` ` object.  
 
 3. Display the `Device::driveLetter` and the `Device::description` properties.

``` cpp
#include <primo/burner/pb.h>
#include <primo/platform/ustring.h>
#include <primo/platform/reference++.h>

#include <iostream>

namespace p = primo;
namespace pb = primo::burner;

void print_device(int index, pb::Device* device)
{
    using namespace std;

    auto description = p::ustring(device->description());
    auto systemPath = p::ustring(device->systemPath());

    cout << "Device : " << index  << endl;
    cout << "  Description  : " << description.str() << endl;
    cout << "  Drive Letter : " << device->driveLetter() << endl;
    cout << "  System Path  : " << systemPath.str() << endl;

    cout << endl;
}

void enumerate_devices(pb::Engine* engine)
{
    // create device enumerator
    auto enumerator = p::make_ref(engine->createDeviceEnumerator());

    for (int i = 0; i < enumerator->count(); i++) {
        // create a device; do not ask for exclusive access
        auto device = p::make_ref(enumerator->createDevice(i, false));
        
        if (device) {
            print_device(i, device.get());
        }
    }
}
```

## Complete C++ code

``` cpp 
#include <primo/burner/pb.h>
#include <primo/platform/ustring.h>
#include <primo/platform/reference++.h>

#include <iostream>

namespace p = primo;
namespace pb = primo::burner;

void print_device(int index, pb::Device* device)
{
    using namespace std;

    auto description = p::ustring(device->description());
    auto systemPath = p::ustring(device->systemPath());

    cout << "Device : " << index  << endl;
    cout << "  Description  : " << description.str() << endl;
    cout << "  Drive Letter : " << device->driveLetter() << endl;
    cout << "  System Path  : " << systemPath.str() << endl;

    cout << endl;
}

void enumerate_devices(pb::Engine* engine)
{
    // create device enumerator
    auto enumerator = p::make_ref(engine->createDeviceEnumerator());

    for (int i = 0; i < enumerator->count(); i++) {
        // create a device; do not ask for exclusive access
        auto device = p::make_ref(enumerator->createDevice(i, false));
        
        if (device) {
            print_device(i, device.get());
        }
    }
}

void enumerate_devices()
{
    // Create engine
    auto engine = p::make_ref(pb::Library::createEngine());

    // Initialize engine
    engine->initialize();

    enumerate_devices(engine.get());

    // terminate engine
    engine->shutdown();
}

int main()
{
    // Set license. To run PrimoBurner in demo mode, comment the next line out
    pb::Library::setLicense("license-xml-string");
    
    pb::Library::enableTraceLog();
    
    enumerate_devices();
    
    pb::Library::disableTraceLog();

    return 0;
}
```
