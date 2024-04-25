---
title: Developing with Visual Studio Code on Windows
metadata:
    description: This page describes the steps needed to setup Visual Studio Code for AVBlocks development on Windows
taxonomy:
    category: docs
---

# Developing with Visual Studio Code on Windows

This topic describes the steps needed to setup Visual Studio Code for AVBlocks development on Windows. These steps have been verified to work on on Windows 11 (22H2).

## Visual Studio Code

Download and install from [Visual Studio Code](https://code.visualstudio.com/download) site.

Open Visual Studio Code and press `Ctrl + Shift + P`. Select `Shell Command: Install 'code' command in PATH`. 

Also install the [C/C++ Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools-extension-pack).

## Create CMake project 

Follow the steps in [Create a Command Line Tool using CMake](create-cpp-command-line-tool-cmake) to create a CMake project.

## Automate the build

Add the following Visual Studio Code specific files to the `.vscode` subdir:

### `.vscode/tasks.json`

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Build",
            "type": "shell",
            "windows": {
                "command": "${workspaceFolder}/build.ps1"
            },
            "group": "build"
        }
    ]
}
```

### `.vscode/launch.json`

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Debug (cppvsdbg)",
            "type": "cppdbg",
            "request": "launch",
            "cwd": "${workspaceFolder}",
            "windows": {
                "type": "cppvsdbg",
                "program": "${workspaceFolder}/build/debug/simple-converter.exe"
            },
            "preLaunchTask": "Build"
        }        
   ]
}
```

## Test the build

Test the build by pressing `Ctrl + Shift + B`. Visual Studio Code should execute the `build.ps1` script automatically.

## Test the debugging

Set a breakpoint on the first line of `int main(int argc, const char *argv[])` inside `src/main.cpp`. Press `F5` to launch the debugger. It should stop at the breakpoint.
