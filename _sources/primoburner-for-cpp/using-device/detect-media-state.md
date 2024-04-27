---
title: Detect Media Presence
metadata:
    description: How to detect whether there is media in a device or not.
---

# Detect Media Presence

Detect whether there is media in a device or not. This is how you do it:

``` cpp
void detect_media_presense(pb::Device* device)
{
    using namespace std;

    // get device description
    auto description = p::ustring(device->description());

    // get media state and unit ready state (aka device status)
    auto media_state = device->mediaState();
    uint32_t unit_ready = device->unitReadyState();

    auto media_state_str = media_state_string(media_state);

    cout << format(" ( {}:) - {}", device->driveLetter(), description.str()) << endl;
    cout << format("  Media State      : {}", media_state_str.str()) << endl;
    cout << format("  Unit Ready State : {:#08x}", unit_ready) << endl;
    
    // get detailed SCSI sense description
    if (unit_ready != 0) {
        ScsiInterface *scsi = device->createScsiInterface();

        // derive SCSI sense from unit ready state    
        ScsiCommandSense sense = {0};
        sense.Key = (unit_ready >> 16) & 0xFF; // second byte
        sense.ASC = (unit_ready >> 8) & 0xFF; // third byte
        sense.ASCQ = unit_ready & 0xFF; // fourth byte

        // get sense message
        auto sense_message = p::ustring(scsi->getSenseMessage(&sense));
        
        cout << format("  SCSI Sense -> Key: {:#04x} ASC: {:#04x} ASCQ: {:#04x} - {}", 
                       sense.Key, sense.ASC, sense.ASCQ, sense_message.str()) << endl;

        scsi->release();
    }
    
    cout << endl;
}
```

For the `media_state_string` function definition see [C++ Utility Classes and Functions](/primoburner-for-cpp/c-utility-classes-and-functions)


## Complete C++ code

``` cpp
#include <primo/burner/pb.h>
#include <primo/platform/ustring.h>
#include <primo/platform/reference++.h>

#include <iostream>
#include <format>

namespace p = primo;
namespace pb = primo::burner;

p::ustring media_state_string(pb::MediaReady::Enum media_state)
{
    switch (media_state) {
    case pb::MediaReady::StateUnknown:
        return "Unknown";
    case pb::MediaReady::NotPresent:
        return "Not Present";
    case pb::MediaReady::Present:
        return "Present";
    default:
        return "Unknown";
    }
}

void detect_media_presense(pb::Device* device)
{
    using namespace std;

    // get device description
    auto description = p::ustring(device->description());

    // get media state and unit ready state (aka device status)
    auto media_state = device->mediaState();
    uint32_t unit_ready = device->unitReadyState();

    auto media_state_str = media_state_string(media_state);

    cout << format(" ( {}:) - {}", device->driveLetter(), description.str()) << endl;
    cout << format("  Media State      : {}", media_state_str.str()) << endl;
    cout << format("  Unit Ready State : {:#08x}", unit_ready) << endl;
    
    // get detailed SCSI sense description
    if (unit_ready != 0) {
        ScsiInterface *scsi = device->createScsiInterface();

        // derive SCSI sense from unit ready state    
        ScsiCommandSense sense = {0};
        sense.Key = (unit_ready >> 16) & 0xFF; // second byte
        sense.ASC = (unit_ready >> 8) & 0xFF; // third byte
        sense.ASCQ = unit_ready & 0xFF; // fourth byte

        // get sense message
        auto sense_message = p::ustring(scsi->getSenseMessage(&sense));
        
        cout << format("  SCSI Sense -> Key: {:#04x} ASC: {:#04x} ASCQ: {:#04x} - {}", 
                       sense.Key, sense.ASC, sense.ASCQ, sense_message.str()) << endl;

        scsi->release();
    }
    
    cout << endl;
}

void detect_media_presense(pb::Engine* engine)
{
    // create device enumerator
    auto enumerator = p::make_ref(engine->createDeviceEnumerator());

    for (int i = 0; i < enumerator->count(); i++) {
        // create a device; do not ask for exclusive access
        auto device = p::make_ref(enumerator->createDevice(i, false));
        
        if (device) {
            detect_media_presense(device.get());
        }
    }
}

void detect_media_presense()
{
    // Create engine
    auto engine = p::make_ref(pb::Library::createEngine());

    // Initialize engine
    engine->initialize();

    detect_media_presense(engine.get());

    // terminate engine
    engine->shutdown();
}

int main()
{
    // Set license. To run PrimoBurner in demo mode, comment the next line out
    pb::Library::setLicense("license-xml-string");
    
    pb::Library::enableTraceLog();
    
    detect_media_presense();
    
    pb::Library::disableTraceLog();

    return 0;
}
```
