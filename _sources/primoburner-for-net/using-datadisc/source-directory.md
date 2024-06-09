---
title: Source Directory
slug: source-directory
taxonomy:
    category: [docs]
    tags: [.NET,DataDisc]
html_meta:
    description: This topic explains how to create the source directory for the BD, DVD, and CD burning samples from the
orphan:
---

# Source Directory

This topic explains how to create the source directory for the BD, DVD, and CD burning samples from the [Using DataDisc](/primoburner-for-net/using-datadisc) section.

For demo purposes, we will use a few video files from the [Internet Archive](https://archive.org/download/WildlifeVideoMedia). The reason for choosing those files is that they are not too small, and not too large, just the right size for demoing most of the DataDisc functionality.

## Windows

You can follow these steps to manually create the source directory on Windows:

1. Create a `DataDisc` subdirectory in your your home directory. On Windows your home directory is typically located at `C:\Users\<yourusername>`.
2. In `DataDisc`, create 4 additional directories: `Elephant`, `Hippo`, `Leopard`, and `Lion`.
3. Download `Elephant.ogv` from the [Internet Archive](https://archive.org/download/WildlifeVideoMedia) and save it in `Elephant`.
4. Download `Hippo.ogv` from the [Internet Archive](https://archive.org/download/WildlifeVideoMedia) and save it in `Hippo`.
5. Download `Leopard.ogv` from the [Internet Archive](https://archive.org/download/WildlifeVideoMedia) and save it in `Leopard`.
6. Download the `Lion.ogv` from the [Internet Archive](https://archive.org/download/WildlifeVideoMedia) and save it in `Lion`.

Alternatively, you can create the source directory by executing these scripts in PowerShell (requires PowerShell 3.0):

Ensure `Get-ExecutionPolicy` is at least `RemoteSigned`:

``` powershell 
start powershell -Verb runAs -ArgumentList "Set-ExecutionPolicy RemoteSigned -Force"
```

Create the `DataDisc` directory and download all files (in PowerShell):

``` powershell 
cd ~

mkdir DataDisc

foreach ($name in "Elephant", "Hippo", "Leopard", "Lion") {
    mkdir DataDisc/$name
    wget https://archive.org/download/WildlifeVideoMedia/$name.ogv `
         -UseBasicParsing `
         -OutFile $pwd/DataDisc/$name/$name.ogv
}
```    

To verify the file structure, run:

``` powershell
tree DataDisc /F /A
```

The output from the tree command should be similar to this:

``` powershell
Folder PATH listing for volume ABC
Volume serial number is 1234-5678
C:\USERS\<YOURUSERNAME>\DATADISC
+---Elephant
|       Elephant.ogv
|
+---Hippo
|       Hippo.ogv
|
+---Leopard
|       Leopard.ogv
|
---Lion
        Lion.ogv
```
