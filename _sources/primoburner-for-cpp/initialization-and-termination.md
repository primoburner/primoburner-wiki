---
title: Initialization and Termination
html_meta:
    description: This topic describes how to initialize and terminate the PrimoBurner API.
taxonomy:
    category: docs
---

# Initialization and Termination

You must initialize the PrimoBurner API when your application starts and terminate it before your application ends. The PrimoBurner license can be set right after the initialization. If you do not set a license, PrimoBurner will work in demo mode.

``` cpp 
#include <primo/burner/pb.h>

using namespace primo::burner;

int main() {
    Library::setLicense("license-xml-string");

    // optionally enable trace logging
    // this will create a file named PrimoBurner.log in user's home directory
    Library::enableTraceLog(nullptr, false);

    Engine *engine = Library::createEngine();
    engine->initialize();

     // Code that uses PrimoBurner goes here

    engine->shutdown();

    // optionally disable trace logging
    Library::disableTraceLog();

    return 0;    
}
```
