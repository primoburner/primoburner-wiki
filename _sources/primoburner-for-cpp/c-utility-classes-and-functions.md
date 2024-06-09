---
title: C++ Utility Classes and Functions
html_meta:
    description: This topic lists the utility classes and functions used by the C++ code snippets on this wiki.
taxonomy:
    category: docs
---

# C++ Utility Classes and Functions

This topic lists the utility classes and functions used by the C++ code snippets on this site.

## Functions

### media_state_string

```cpp
#include <primo/burner/pb.h>
#include <primo/platform/ustring.h>
#include <primo/platform/reference++.h>

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
```

### media_is_fully_formatted

``` cpp
#include <primo/burner/pb.h>
#include <primo/platform/ustring.h>
#include <primo/platform/reference++.h>

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
```

### media_profile_string

```cpp
#include <primo/burner/pb.h>
#include <primo/platform/ustring.h>
#include <primo/platform/reference++.h>

namespace p = primo;
namespace pb = primo::burner;

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
```
