---
title: Setting License Info
metadata:
    description: This topic describes how to use a PrimoBurner license file.
taxonomy:
    category: docs
orphan:
---

# Setting License Info

This topic describes how to use a PrimoBurner license file.

## PrimoBurner License File

The PrimoBurner license file is sent to you via email when you purchase a license. The license file is an XML document that looks similar to this one:

``` xml 

<!-- 
This file contains PrimoSoftware license(s). 
Line breaks and indentation between elements can be edited, 
but any other reformatting may invalidate the license(s). 
-->
<primoSoftware>
    <license version='1.0'>
    <token>abcdefghijklmnlo</token>
    <revision>1</revision>
    <issueDate>2013-05-29</issueDate>
    <expireDate>2015-05-30</expireDate>
    <updateTime>2014-11-02 22:59:08</updateTime>
    <item id='pb-win'>
        <product>pb</product>
    </item>
    <item id='avb-win'>
        <product>avb</product>
        <feature id='aac-dec' />
        <feature id='aac-enc' />
        <feature id='mpa-dec' />
        <feature id='mpa-enc' />
        <feature id='vorbis-dec' />
        <feature id='vorbis-enc' />
        <feature id='wma-dec' />
        <feature id='wma-enc' />
        <feature id='pcm' />
    </item>
    <signature>very_very_long_signature_string_here</signature>
    </license>
</primoSoftware>
```

You have to pass the XML as a string to Library.SetLicense.

## Example

`````{tab-set}

````{tab-item} C&#35;
In C#, it is possible to use a string literal, e.g. `@"xml goes here"`:    

```{code-block} csharp

using System;
using System.Diagnostics;

using PrimoSoftware.Burner;

namespace SetLicense
{
    class Program
    {
        private const string licenseXml = 
            @"
            <!-- 
            This file contains PrimoSoftware license(s). 
            Line breaks and indentation between elements can be edited, 
            but any other reformatting may invalidate the license(s). 
            -->
            <primoSoftware>
                <license version='1.0'>
                <token>abcdefghijklmnlo</token>
                <revision>1</revision>
                <issueDate>2013-05-29</issueDate>
                <expireDate>2015-05-30</expireDate>
                <updateTime>2014-11-02 22:59:08</updateTime>
                <item id='pb-win'>
                    <product>pb</product>
                </item>
                <item id='avb-win'>
                    <product>avb</product>
                    <feature id='aac-dec' />
                    <feature id='aac-enc' />
                    <feature id='mpa-dec' />
                    <feature id='mpa-enc' />
                    <feature id='vorbis-dec' />
                    <feature id='vorbis-enc' />
                    <feature id='wma-dec' />
                    <feature id='wma-enc' />
                    <feature id='pcm' />
                </item>
                <signature>very_very_long_signature_string_here</signature>
                </license>
            </primoSoftware>
            ";

        static void Main(string[] args)
        {
            Library.Initialize();

            Library.SetLicense(licenseXml);
            Debug.Assert(LicenseStatusFlags.Ready == Library.LicenseStatus);

            Library.Shutdown();
        }
    }
}
```
````

````{tab-item} Visual Basic

In VB, you can use XElement and [XML literals](https://msdn.microsoft.com/en-us/library/bb384629.aspx). Note that, in order for this to work, you have to remove the comment from the top of the XML text:

```{code-block} vbnet
Imports PrimoSoftware.Burner

Class Program

    Private Shared licenseXml As XElement =
            <primoSoftware>
                <license version='1.0'>
                <token>abcdefghijklmnlo</token>
                <revision>1</revision>
                <issueDate>2013-05-29</issueDate>
                <expireDate>2015-05-30</expireDate>
                <updateTime>2014-11-02 22:59:08</updateTime>
                <item id='pb-win'>
                    <product>pb</product>
                </item>
                <item id='avb-win'>
                    <product>avb</product>
                    <feature id='aac-dec' />
                    <feature id='aac-enc' />
                    <feature id='mpa-dec' />
                    <feature id='mpa-enc' />
                    <feature id='vorbis-dec' />
                    <feature id='vorbis-enc' />
                    <feature id='wma-dec' />
                    <feature id='wma-enc' />
                    <feature id='pcm' />
                </item>
                <signature>very_very_long_signature_string_here</signature>
                </license>
            </primoSoftware>

    Public Shared Sub Main()
        Library.Initialize()

        Library.SetLicense(licenseXml.ToString())
        Debug.Assert(LicenseStatusFlags.Ready = Library.LicenseStatus)

        Library.Shutdown()
    End Sub

End Class
```
````

`````
