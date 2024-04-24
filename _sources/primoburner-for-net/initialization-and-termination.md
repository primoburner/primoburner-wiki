---
title: Initialization and Termination
metadata:
    description: This topic describes how to initialize and terminate the PrimoBurner API.
taxonomy:
    category: docs
orphan:
---

# Initialization and Termination

You must initialize the PrimoBurner API when your application starts and terminate it before your application ends. The PrimoBurner license can be set right after the initialization. If you do not set a license, PrimoBurner will work in demo mode.

``` csharp 
using PrimoSoftware.Burner;

namespace InitAndTerminate
{
    class Program
    {
        static void Main(string[] args)
        {
            Library.Initialize();

            // Set license. To run PrimoBurner in demo mode, comment the next line out
            Library.SetLicense("license-xml-string");

            // Code that uses PrimoBurner goes here

            Library.Shutdown();
        }
    }
}
```
