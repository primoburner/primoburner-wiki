---
title: Detect Media Presence
metadata:
    description: How to detect whether there is media in a device or not.
---

# Detect Media Presence

Detect whether there is media in a device or not. This is how you do it:

``` csharp
private static void DetectMediaPresence(Device device)
{
    Console.WriteLine("({0}:) - {1}", device.DriveLetter, device.Description);

    Console.WriteLine(" Media State : {0}", device.MediaState);
    Console.WriteLine(" Unit Ready  : {0}", (DeviceError)device.UnitReady);

    Console.WriteLine(); Console.WriteLine();
}
```

## Complete .NET code

``` csharp
using System;
    
using PrimoSoftware.Burner;

namespace DetectMediaPresence
{
    class Program
    {
        private static void DetectMediaPresence(Device device)
        {
            Console.WriteLine("({0}:) - {1}", device.DriveLetter, device.Description);

            Console.WriteLine(" Media State : {0}", device.MediaState);
            Console.WriteLine(" Unit Ready  : {0}", (DeviceError)device.UnitReady);

            Console.WriteLine(); Console.WriteLine();
        }

        private static void DetectMediaPresence(Engine engine)
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
                        DetectMediaPresence(device);
                    }
                }
            }
        }

        private static void DetectMediaPresence()
        {
            // Create engine
            using (var engine = new Engine())
            {
                // Initialize the engine
                engine.Initialize();

                DetectMediaPresence(engine);

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

            // presence 
            DetectMediaPresence(); 

            Library.Shutdown();
        }
    }
}
```
