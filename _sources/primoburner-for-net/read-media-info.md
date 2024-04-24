---
title: Read Media Info
taxonomy:
    category: [docs]
    tags: [.NET,BDMediaInfo,Device,DVDMediaInfo,DVDMinusMediaInfo,DVDPlusMediaInfo,MediaInfo]
metadata:
    description: How to read media information such as manufacturer id, and media type id
orphan:
---

# Read Media Info

Read media information such as manufacturer id, and media type id.

```csharp

private static void ReadMediaInfo(Device device)
{
    Console.WriteLine("({0}:) - {1}", device.DriveLetter, device.Description);

    MediaProfile mp = device.MediaProfile;
    Console.WriteLine(" Media Profile : {0}", mp.ToString()); 

    MediaInfo info = device.ReadMediaInfo();
    if (null != info) 
    {
        // Is it Blu-ray media?
        if (device.MediaIsBD)
        {
            BDMediaInfo bd = info.BDInfo;

            Console.WriteLine(" Manufacturer ID  : {0}", bd.ManufacturerID);
            Console.WriteLine(" Media Type ID    : {0}", bd.MediaTypeID);
            Console.WriteLine(" Product Revision : {0}", bd.ProductRevision);
        }

        // Is it DVD media?
        if (device.MediaIsDVD)
        {
            DVDMediaInfo dvd = info.DVDInfo;

            // Is it DVD Plus media?
            if (MediaProfile.DvdPlusR == mp || 
                MediaProfile.DvdPlusRDL == mp ||
                MediaProfile.DvdPlusRw == mp || 
                MediaProfile.DvdPlusRwDL == mp)
            {
                DVDPlusMediaInfo dvdPlus = dvd.PlusInfo;

                Console.WriteLine(" Manufacturer ID  : {0}", dvdPlus.ManufacturerID);
                Console.WriteLine(" Media Type ID    : {0}", dvdPlus.MediaTypeID);
                Console.WriteLine(" Product Revision : {0}", dvdPlus.ProductRevision);
            }

            // Is it DVD Minus media?
            if (MediaProfile.DvdMinusRSeq == mp || 
                MediaProfile.DvdMinusRDLSeq == mp || 
                MediaProfile.DvdMinusRDLJump == mp ||
                MediaProfile.DvdMinusRwSeq == mp || 
                MediaProfile.DvdMinusRwRo == mp)
            {
                DVDMinusMediaInfo dvdMinus = dvd.MinusInfo;

                Console.Write(" Manufacturer ID 1 : {0}n", dvdMinus.ManufacturerID1);
                Console.Write(" Manufacturer ID 2 : {0}n", dvdMinus.ManufacturerID2);
                Console.Write(" Manufacturer ID 3 : ", dvdMinus.ManufacturerID3);
                {
                    foreach (byte mid3 in dvdMinus.ManufacturerID3)
                        Console.Write("0x{0:x2} ", mid3);
                }
                Console.WriteLine();
            }
        }
    }

    Console.WriteLine(); Console.WriteLine();
}
```

## Complete .NET code

``` csharp
using System;
    
using PrimoSoftware.Burner;

namespace ReadMediaInfo
{
    class Program
    {
        private static void ReadMediaInfo(Device device)
        {
            Console.WriteLine("({0}:) - {1}", device.DriveLetter, device.Description);

            MediaProfile mp = device.MediaProfile;
            Console.WriteLine(" Media Profile : {0}", mp.ToString()); 

            MediaInfo info = device.ReadMediaInfo();
            if (null != info) 
            {
                // Is it Blu-ray media?
                if (device.MediaIsBD)
                {
                    BDMediaInfo bd = info.BDInfo;

                    Console.WriteLine(" Manufacturer ID  : {0}", bd.ManufacturerID);
                    Console.WriteLine(" Media Type ID    : {0}", bd.MediaTypeID);
                    Console.WriteLine(" Product Revision : {0}", bd.ProductRevision);
                }

                // Is it DVD media?
                if (device.MediaIsDVD)
                {
                    DVDMediaInfo dvd = info.DVDInfo;

                    // Is it DVD Plus media?
                    if (MediaProfile.DvdPlusR == mp || 
                        MediaProfile.DvdPlusRDL == mp ||
                        MediaProfile.DvdPlusRw == mp || 
                        MediaProfile.DvdPlusRwDL == mp)
                    {
                        DVDPlusMediaInfo dvdPlus = dvd.PlusInfo;

                        Console.WriteLine(" Manufacturer ID  : {0}", dvdPlus.ManufacturerID);
                        Console.WriteLine(" Media Type ID    : {0}", dvdPlus.MediaTypeID);
                        Console.WriteLine(" Product Revision : {0}", dvdPlus.ProductRevision);
                    }

                    // Is it DVD Minus media?
                    if (MediaProfile.DvdMinusRSeq == mp || 
                        MediaProfile.DvdMinusRDLSeq == mp || 
                        MediaProfile.DvdMinusRDLJump == mp ||
                        MediaProfile.DvdMinusRwSeq == mp || 
                        MediaProfile.DvdMinusRwRo == mp)
                    {
                        DVDMinusMediaInfo dvdMinus = dvd.MinusInfo;

                        Console.Write(" Manufacturer ID 1 : {0}n", dvdMinus.ManufacturerID1);
                        Console.Write(" Manufacturer ID 2 : {0}n", dvdMinus.ManufacturerID2);
                        Console.Write(" Manufacturer ID 3 : ", dvdMinus.ManufacturerID3);
                        {
                            foreach (byte mid3 in dvdMinus.ManufacturerID3)
                                Console.Write("0x{0:x2} ", mid3);
                        }
                        Console.WriteLine();
                    }
                }
            }

            Console.WriteLine(); Console.WriteLine();
        }

        private static void ReadMediaInfo(Engine engine)
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
                        ReadMediaInfo(device);
                    }
                }
            }
        }

        private static void ReadMediaInfo()
        {
            // Create engine
            using (var engine = new Engine())
            {
                // Initialize the engine
                engine.Initialize();

                ReadMediaInfo(engine);

                // terminate engine
                engine.Shutdown();
            }
        }

        static void Main(string[] args)
        {
            // Initialize PrimoBurner
            Library.Initialize();

            // Set license. To run PrimoBurner in demo mode, comment the next line out
            Library.SetLicense("license-xml-string");

            // read media info 
            ReadMediaInfo(); 

            Library.Shutdown();
        }
    }
}    
```
 