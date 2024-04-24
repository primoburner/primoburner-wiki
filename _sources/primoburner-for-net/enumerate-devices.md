---
title: Enumerate Devices
metadata:
    description: How to enumerate the optical devices connected to your system.
taxonomy:
    category: [docs]
    tags: [Device,DeviceEnumerator,Engine]
orphan:
---

# Enumerate Devices

Enumerate the optical devices connected to your system.

To enumerate the available devices:

 1. Obtain a DeviceEnumerator object by calling the Engine.CreateDeviceEnumerator method.  

 2. Use the DeviceEnumerator.CreateDevice method to create a Device object.  
 
 3. Display the Device.DriveLetter and the Device.Description properties.

``` csharp

private static void EnumerateDevices(Engine engine)
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
        }
    }
}

```

## Complete .NET code

``` csharp 

using System;

using PrimoSoftware.Burner;

namespace EnumerateDevices
{
    class Program
    {
        private static void EnumerateDevices(Engine engine)
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
                }
            }
        }

        private static void EnumerateDevices()
        {
            // Create engine
            using (var engine = new Engine())
            {
                // Initialize engine
                engine.Initialize();

                EnumerateDevices(engine);

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

            EnumerateDevices();

            Library.Shutdown();
        }
    }
}

```
