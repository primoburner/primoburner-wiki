---
title: Burn Directory to DVD+R
slug: burn-directory-to-dvd-plus-r
taxonomy:
    category: [docs]
    tags: [.NET,DataDisc,Device,DeviceEnumerator,DVD+R,Engine,Library,UnitReady]
html_meta:
    description: How to  burn the contents of a directory to a blank DVD+R disc.
orphan:
---

# Burn Directory to DVD+R

Burn the contents of a directory to a blank DVD+R disc.

## Source Directory

The sample code in this article assumes a directory named `DataDisc` exists under your user home directory. To create the `DataDisc` directory follow the steps listed in the [Source Directory](/primoburner-for-net/using-datadisc/source-directory) article.

## Code

The following function takes a Device object and path to a directory and burns the contents of the directory to a blank DVD+R disc.

``` csharp

private static void BurnDirToDVDPlusR(Device device, string sourceDirectory)
{
    if (!CheckDeviceAndMedia(device))
    {
        Console.WriteLine("Please insert a blank DVD+R in burner and try again.");

        // media is not DVD+R, or is not blank
        device.Eject(true, true);

        return;
    }

    // Use DataDisc to burn the DVD image layout
    using (var dataDisc = new DataDisc())
    {
        // set device, and write method
        dataDisc.Device = device;

        // WriteMethod.DvdIncremental method is recommended for data 
        dataDisc.WriteMethod = WriteMethod.DvdIncremental;

        // close the disc for true playback compatibility
        // this will result in slower disc burning
        dataDisc.CloseDisc = true;

        // DataDisc needs this to create the correct disc image
        dataDisc.ImageType = ImageType.Udf;

        // set volume label and creation date
        dataDisc.UdfVolumeProps.VolumeLabel = "DATADVDPLUS";
        dataDisc.UdfVolumeProps.CreationTime = DateTime.Now;

        // set the image layout from the source directory
        dataDisc.SetImageLayoutFromFolder(sourceDirectory);

        // this may take some time
        dataDisc.WriteToDisc(true);
    }
}

```

## Complete .NET Code

``` csharp

using System;

using PrimoSoftware.Burner;
using System.IO;

namespace EnumerateDevices
{
    class Program
    {
        static void Main(string[] args)
        {
            // Initialize PrimoBurner
            Library.Initialize();

            // Set license. To run PrimoBurner in demo mode, comment the next line out
            Library.SetLicense("license-xml-string");

            string sourceDirectory = GetSourceDirectory();
            BurnDirToDVDPlusR(sourceDirectory);

            Library.Shutdown();
        }

        private static void BurnDirToDVDPlusR(string sourceDirectory)
        {
            // Create engine
            using (var engine = new Engine())
            {
                // Initialize engine
                engine.Initialize();

                BurnDirToDVDPlusR(engine, sourceDirectory);

                // terminate engine
                engine.Shutdown();
            }
        }

        private static void BurnDirToDVDPlusR(Engine engine, string sourceDirectory)
        {
            // create device enumerator
            using (var enumerator = engine.CreateDeviceEnumerator())
            {
                for (int i = 0; i < enumerator.Count; i++)
                {
                    // create a device; do not ask for exclusive access
                    var device = enumerator.CreateDevice(i, false);
                    if (null != device)
                    {
                        Console.WriteLine("({0}:) - {1}",
                            device.DriveLetter, device.Description);
                    }

                    device.Dispose();
                }

                BurnDirToDVDPlusR(enumerator, sourceDirectory);
            }
        }

        private static void BurnDirToDVDPlusR(DeviceEnumerator enumerator, 
                                                        string sourceDirectory)
        {
            // Select the first optical drive
            int index = 0;

            // You could also get an index from a drive letter, e.g.:
            // int index = Library.GetCDROMIndexFromLetter('E');

            // create a device; this time _ask_ for exclusive access
            var device = enumerator.CreateDevice(index, true);
            if (null != device)
            {
                BurnDirToDVDPlusR(device, sourceDirectory);

                device.Dispose();
            }
        }

        private static void BurnDirToDVDPlusR(Device device, string sourceDirectory)
        {
            if (!CheckDeviceAndMedia(device))
            {
                Console.WriteLine("Please insert a blank DVD+R in burner and try again.");

                // media is not DVD+R, or is not blank
                device.Eject(true, true);

                return;
            }

            // Use DataDisc to burn the DVD image layout
            using (var dataDisc = new DataDisc())
            {
                // set device, and write method
                dataDisc.Device = device;

                // WriteMethod.DvdIncremental method is recommended for data 
                dataDisc.WriteMethod = WriteMethod.DvdIncremental;

                // close the disc for true playback compatibility
                // this will result in slower disc burning
                dataDisc.CloseDisc = true;

                // DataDisc needs this to create the correct disc image
                dataDisc.ImageType = ImageType.Udf;

                // set volume label and creation date
                dataDisc.UdfVolumeProps.VolumeLabel = "DATADVDPLUS";
                dataDisc.UdfVolumeProps.CreationTime = DateTime.Now;

                // set the image layout from the source directory
                dataDisc.SetImageLayoutFromFolder(sourceDirectory);

                // this may take some time
                dataDisc.WriteToDisc(true);
            }
        }

        private static bool CheckDeviceAndMedia(Device device)
        {
            // close the device tray and refresh disc information
            if (device.Eject(false))
            {
                // wait for the device to become ready
                WaitForUnitReady(device);

                // refresh disc information. Need to call this method when media changes
                device.Refresh();
            }

            // check if disc is present
            if (MediaReady.Present != device.MediaState)
                return false;

            // check if disc is blank
            if (!device.MediaIsBlank)
                return false;

            // for simplicity only accept DVD+R
            MediaProfile mp = device.MediaProfile;
            if (MediaProfile.DvdPlusR != mp)
                return false;

            return true;
        }

        private static void WaitForUnitReady(Device device)
        {
            int error = device.UnitReady;
            while (true)
            {
                if ((int)DeviceError.Success == error)
                    break;

                if ((int)DeviceError.MediumNotPresent == error)
                    break;

                if ((int)DeviceError.MediumNotPresentTrayClosed == error)
                    break;

                if ((int)DeviceError.MediumNotPresentTrayOpen == error)
                    break;

                System.Threading.Thread.Sleep(1000);
                error = device.UnitReady;
            }
        }

        private static string GetSourceDirectory()
        {
            // Assuming to be "C:/Users/<yourusername>/DataDisc"
            string homeDir = Environment.GetFolderPath(Environment.SpecialFolder.UserProfile);
            return Path.Combine(homeDir, "DataDisc");
        }
    }

}

```
