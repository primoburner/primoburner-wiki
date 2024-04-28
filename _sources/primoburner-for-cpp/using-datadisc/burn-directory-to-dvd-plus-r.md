---
title: Burn Directory to DVD+R
slug: burn-directory-to-dvd-plus-r
taxonomy:
    category: [docs]
    tags: [DataDisc,Device,DeviceEnumerator,DVD+R,Engine,Library,UnitReady]
metadata:
    description: How to  burn the contents of a directory to a blank DVD+R disc.
---

# Burn Directory to DVD+R

Burn the contents of a directory to a blank DVD+R disc.

## Source Directory

The sample code in this article assumes a directory named `DataDisc` exists under your user home directory. To create the `DataDisc` directory follow the steps listed in the [Source Directory](/primoburner-for-cpp/using-datadisc/source-directory) article.

## Code

The following function takes a Device object and path to a directory and burns the contents of the directory to a blank DVD+R disc.

``` cpp
void burn_dir_to_dvd_plus_r(pb::Device* device, std::string source_dir)
{
    using namespace std;

    if (!check_device_and_media(device))
    {
        cout << "Please insert a blank DVD+R in burner and try again." << endl;

        // media is not DVD+R, or is not blank
        device->eject(true, true);

        return;
    }

    // Use DataDisc to burn the DVD image layout
    {
        auto dataDisc = p::make_ref(pb::Library::createDataDisc());

        // set device, and write method
        dataDisc->setDevice(device);

        // WriteMethod.DvdIncremental method is recommended for data 
        dataDisc->setWriteMethod(pb::WriteMethod::DVDIncremental);

        // close the disc for true playback compatibility
        // this will result in slower disc burning
        dataDisc->setCloseDisc(true);

        // DataDisc needs this to create the correct disc image
        dataDisc->setImageType(pb::ImageTypeFlags::Udf);

        // set volume label and creation date
        auto volume_label = p::ustring("DATADVDPLUS");
        dataDisc->udfVolumeProps()->setVolumeLabel(volume_label.u16());

        auto now = chrono::system_clock::now();
        time_t now_time = chrono::system_clock::to_time_t(now);
        dataDisc->udfVolumeProps()->setVolumeCreationTime(now_time);

        // set the image layout from the source directory
        auto source_dir_ustr = p::ustring(source_dir);
        dataDisc->setImageLayoutFromFolder(source_dir_ustr.u16());

        // this may take some time
        dataDisc->writeToDisc(true);
    }
}
```

## Complete C++ Code

``` cpp
#include "primo/burner/pb_api.h"
#include <primo/burner/pb.h>
#include <primo/burner/pb_lib.h>
#include <primo/scsi/scsi2.h>

#include <primo/platform/ustring.h>
#include <primo/platform/reference++.h>

#include <cstdlib>

#include <chrono>
#include <iostream>
#include <filesystem>
#include <format>
#include <thread>

namespace p = primo;
namespace pb = primo::burner;
namespace ps = primo::scsi;

void wait_for_unit_ready(pb::Device* device)
{
    using namespace std;

    uint32_t error = device->unitReadyState();
    while (true)
    {
        if (ps::ScsiSense::Success == error)
            break;

        if (ps::ScsiSense::MediumNotPresent == error)
            break;

        if (ps::ScsiSense::MediumNotPresentTrayClosed == error)
            break;

        if (ps::ScsiSense::MediumNotPresentTrayOpen == error)
            break;

        this_thread::sleep_for(chrono::milliseconds(1000));    
        
        error = device->unitReadyState();
    }
}

bool check_device_and_media(pb::Device* device)
{
    // close the device tray and refresh disc information
    if (device->eject(false))
    {
        // wait for the device to become ready
        wait_for_unit_ready(device);

        // refresh disc information. Need to call this method when media changes
        device->refresh();
    }

    // check if disc is present
    if (pb::MediaReady::Present != device->mediaState())
        return false;

    // check if disc is blank
    if (!device->isMediaBlank())
        return false;

    // for simplicity only accept DVD+R
    pb::MediaProfile::Enum mp = device->mediaProfile();
    if (pb::MediaProfile::DVDPlusR != device->mediaProfile())
        return false;

    return true;
}

void burn_dir_to_dvd_plus_r(pb::Device* device, std::string source_dir)
{
    using namespace std;

    if (!check_device_and_media(device))
    {
        cout << "Please insert a blank DVD+R in burner and try again." << endl;

        // media is not DVD+R, or is not blank
        device->eject(true, true);

        return;
    }

    // Use DataDisc to burn the DVD image layout
    {
        auto dataDisc = p::make_ref(pb::Library::createDataDisc());

        // set device, and write method
        dataDisc->setDevice(device);

        // WriteMethod.DvdIncremental method is recommended for data 
        dataDisc->setWriteMethod(pb::WriteMethod::DVDIncremental);

        // close the disc for true playback compatibility
        // this will result in slower disc burning
        dataDisc->setCloseDisc(true);

        // DataDisc needs this to create the correct disc image
        dataDisc->setImageType(pb::ImageTypeFlags::Udf);

        // set volume label and creation date
        auto volume_label = p::ustring("DATADVDPLUS");
        dataDisc->udfVolumeProps()->setVolumeLabel(volume_label.u16());

        auto now = chrono::system_clock::now();
        time_t now_time = chrono::system_clock::to_time_t(now);
        dataDisc->udfVolumeProps()->setVolumeCreationTime(now_time);

        // set the image layout from the source directory
        auto source_dir_ustr = p::ustring(source_dir);
        dataDisc->setImageLayoutFromFolder(source_dir_ustr.u16());

        // this may take some time
        dataDisc->writeToDisc(true);
    }
}

void burn_dir_to_dvd_plus_r(pb::DeviceEnum* enumerator, std::string source_dir)
{
    // Select the first optical drive
    int device_index = 0;

    // You could also get an index from a drive letter, e.g.:
    // int device_index = pb::Library::driveLetterToDeviceIndex('E');

    // create a device; this time _ask_ for exclusive access
    auto device = p::make_ref(enumerator->createDevice(device_index, true));
        
    if (device) {
        burn_dir_to_dvd_plus_r(device.get(), source_dir);
    }
}


void burn_dir_to_dvd_plus_r(pb::Engine* engine, std::string source_dir)
{
    using namespace std;

    // create device enumerator
    auto enumerator = p::make_ref(engine->createDeviceEnumerator());

    for (int i = 0; i < enumerator->count(); i++) {
        // create a device; do not ask for exclusive access
        auto device = p::make_ref(enumerator->createDevice(i, false));
        
        if (device) {
            auto description = p::ustring(device->description());
            cout << format(" ( {}:) - {}", device->driveLetter(), description.str()) << endl;
        }
    }

    burn_dir_to_dvd_plus_r(enumerator.get(), source_dir);
}

void burn_dir_to_dvd_plus_r(std::string source_dir)
{
    // Create engine
    auto engine = p::make_ref(pb::Library::createEngine());

    // Initialize engine
    engine->initialize();

    burn_dir_to_dvd_plus_r(engine.get(), source_dir);

    // terminate engine
    engine->shutdown();
}


std::filesystem::path get_source_directory()
{
    const char* homeDir = std::getenv("HOME");
    if (homeDir != nullptr) {
        return std::filesystem::path(homeDir) / "DataDisc";
    }
    
    return std::filesystem::path();    
}

int main()
{
    // Set license. To run PrimoBurner in demo mode, comment the next line out
    pb::Library::setLicense("license-xml-string");
    
    pb::Library::enableTraceLog();
    
    auto source_dir = get_source_directory();
    if (source_dir.empty()) {
        std::cout << "Could not get user's home directory." << std::endl;
        return 1;
    }

    burn_dir_to_dvd_plus_r(source_dir.c_str());
    
    pb::Library::disableTraceLog();

    return 0;
}
```
