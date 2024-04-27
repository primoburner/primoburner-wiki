---
title: Read Media Info
taxonomy:
    category: [docs]
    tags: [BDMediaInfo,Device,DVDMediaInfo,DVDMinusMediaInfo,DVDPlusMediaInfo,MediaInfo]
metadata:
    description: How to read media information such as manufacturer id, and media type id
---

# Read Media Info

Read media information such as manufacturer id, and media type id.

## The `main` function

Start by creating the main function:

```cpp
#include <primo/burner/pb.h>
#include <primo/platform/ustring.h>
#include <primo/platform/reference++.h>

#include <iostream>

namespace p = primo;
namespace pb = primo::burner;

int main()
{
    // Set license. To run PrimoBurner in demo mode, comment the next line out
    pb::Library::setLicense("license-xml-string");
    
    pb::Library::enableTraceLog();
    
    read_media_info();
    
    pb::Library::disableTraceLog();

    return 0;
}
```

## Create `Engine` object

Create `Engine` object and initialize it:

```cpp
void read_media_info()
{
    // Create engine
    auto engine = p::make_ref(pb::Library::createEngine());

    // Initialize engine
    engine->initialize();

    read_media_info(engine.get());

    // terminate engine
    engine->shutdown();
}
```

## Enumerate devices

Enumerate devices and read media info for each device

```cpp
void read_media_info(pb::Engine* engine)
{
    // create device enumerator
    auto enumerator = p::make_ref(engine->createDeviceEnumerator());

    for (int i = 0; i < enumerator->count(); i++) {
        // create a device; do not ask for exclusive access
        auto device = p::make_ref(enumerator->createDevice(i, false));
        
        if (device) {
            read_media_info(device.get());
        }
    }
}
``` 

## Read media info

Get various media properties and print them to the console:

```cpp
void read_media_info(pb::Device* device)
{
    using namespace std;

    auto description = p::ustring(device->description());
    auto systemPath = p::ustring(device->systemPath());

    auto media_profile = device->mediaProfile();

    cout << "  (" << device->driveLetter() << ":) - " << description.str() << endl;
    cout << "  Media Profile : " << media_profile_string(device, media_profile) << endl;

    cout << endl;
}
```

For the `media_profile_string` function definition see [C++ Utility Classes and Functions](/primoburner-for-cpp/c-utility-classes-and-functions)


## Complete C++ Code

This is the complete C++ code including all supporting functions:

```cpp
#include <primo/burner/pb.h>
#include <primo/platform/ustring.h>
#include <primo/platform/reference++.h>

#include <iostream>

namespace p = primo;
namespace pb = primo::burner;

const bool media_is_fully_formatted(pb::Device* device)
{
    assert(device != nullptr);

	// Get media profile
	auto mp = device->mediaProfile();

	// DVD+RW
	if (pb::MediaProfile::DVDPlusRW == mp)
		return (pb::BgFormatStatus::Completed == device->bgFormatStatus());

	// DVD-RW for Restricted Overwrite
	if (pb::MediaProfile::DVDMinusRWRO == mp)
		return device->mediaCapacity() == device->mediaFreeSpace();

	// BD-RE
	if (pb::MediaProfile::BDRE == mp || pb::MediaProfile::BDRSrm == mp || pb::MediaProfile::BDRSrmPow == mp || pb::MediaProfile::BDRRrm == mp)
		return 1 == device->isMediaFormatted();

	return false;
}

p::ustring media_profile_string(pb::Device* device, pb::MediaProfile::Enum media_profile) {
    switch(media_profile) {
        case pb::MediaProfile::CDRom:
            return p::ustring("CD-ROM. Read only CD."); 

        case pb::MediaProfile::CDR:
            return p::ustring("CD-R. Write once CD."); 

        case pb::MediaProfile::CDRW:
            return p::ustring("CD-RW. Re-writable CD.");

        case pb::MediaProfile::DVDRom:
            return p::ustring("DVD-ROM. Read only DVD.");

        case pb::MediaProfile::DVDMinusRSeq:
            return p::ustring("DVD-R for Sequential Recording.");

        case pb::MediaProfile::DVDMinusRDLSeq:	
            return p::ustring("DVD-R DL Double Layer for Sequential Recording.");

        case pb::MediaProfile::DVDMinusRDLJump:
            return p::ustring("DVD-R DL Double Layer for Layer Jump Recording.");

        case pb::MediaProfile::DVDRam:
            return p::ustring("DVD-RAM ReWritable DVD.");

        case pb::MediaProfile::DVDMinusRWRO:
            return p::ustring("DVD-RW for Restricted Overwrite.");

        case pb::MediaProfile::DVDMinusRWSeq:
            return p::ustring("DVD-RW for Sequential Recording.");

        case pb::MediaProfile::DVDPlusRW :
        {
            assert(device != nullptr);
        
            auto fmt = device->bgFormatStatus();
            switch(fmt)
            {
                case pb::BgFormatStatus::NotFormatted:
                    return p::ustring("DVD+RW. Not formatted.");
                break;
                case pb::BgFormatStatus::Partial:
                    return p::ustring("DVD+RW. Partially formatted.");
                break;
                case pb::BgFormatStatus::Pending:
                    return p::ustring("DVD+RW. Background format is pending ...");
                break;
                case pb::BgFormatStatus::Completed:
                    return p::ustring("DVD+RW. Formatted.");
                break;
            }

            return p::ustring("DVD+RW for Random Recording.");
        }

        case pb::MediaProfile::DVDPlusR:
            return p::ustring("DVD+R for Sequential Recording.");
        
        case pb::MediaProfile::DVDPlusRDL:
            return p::ustring("DVD+R DL Double Layer for Sequential Recording.");

        case pb::MediaProfile::BDRom:
            return p::ustring("BD-ROM Read only Blu-ray Disc.");

        case pb::MediaProfile::BDRSrm:
        {
            assert(device != nullptr);

            if (media_is_fully_formatted(device))
                return p::ustring("BD-R in Sequential Recording Mode with Spare Areas (SRM-POW).");

            return p::ustring("BD-R in Sequential Recording Mode.");
        }

        case pb::MediaProfile::BDRSrmPow:
            return p::ustring("BD-R in Sequential Recording Mode with Pseudo-Overwrite (SRM+POW).");

        case pb::MediaProfile::BDRRrm:
            return p::ustring("BD-R in Random Recording Mode (RRM).");

        case pb::MediaProfile::BDRE:
        {
            if (media_is_fully_formatted(device))
                return p::ustring("BD-RE ReWritable Blu-ray Disc. Formatted.");
            
            return p::ustring("BD-RE ReWritable Blu-ray Disc. Blank. Not formatted."); 
        }
        default:
            return p::ustring("Unknown Profile.");
    }
}

void read_media_info(pb::Device* device)
{
    using namespace std;

    auto description = p::ustring(device->description());
    auto systemPath = p::ustring(device->systemPath());

    auto media_profile = device->mediaProfile();

    cout << "  (" << device->driveLetter() << ":) - " << description.str() << endl;
    cout << "  Media Profile : " << media_profile_string(device, media_profile) << endl;

    cout << endl;
}

void read_media_info(pb::Engine* engine)
{
    // create device enumerator
    auto enumerator = p::make_ref(engine->createDeviceEnumerator());

    for (int i = 0; i < enumerator->count(); i++) {
        // create a device; do not ask for exclusive access
        auto device = p::make_ref(enumerator->createDevice(i, false));
        
        if (device) {
            read_media_info(device.get());
        }
    }
}

void read_media_info()
{
    // Create engine
    auto engine = p::make_ref(pb::Library::createEngine());

    // Initialize engine
    engine->initialize();

    read_media_info(engine.get());

    // terminate engine
    engine->shutdown();
}

int main()
{
    // Set license. To run PrimoBurner in demo mode, comment the next line out
    pb::Library::setLicense("license-xml-string");
    
    pb::Library::enableTraceLog();
    
    read_media_info();
    
    pb::Library::disableTraceLog();

    return 0;
}
```