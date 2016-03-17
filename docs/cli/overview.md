# 🔧 .NET Core Command Line Tools (CLI)

## What is the .NET CLI?
.NET Core Command Line Interface is a new foundational cross-platform toolchain for developing 
.NET Core applications. It is "foundational" because it is the primary layer on which other, 
higher-level experiences, such as Integrated Development Environments (IDEs), editors and 
build orchestrators can build on. 

## Motivation for a new toolset
TBD

## Installation
Depending on your scenario, you can either use the native installers to install the CLI or use the 
installation shell script.

The native installers are meant for development environments, that is, single development boxes. They 
are built using the primary installation technology of each platform. 

### What comes in the box?
The following commands come in the installers:

* [new](dotnet-new.md)
* [restore](dotnet-restore.md)
* [run](dotnet-run.md)
* [build](dotnet-build.md)
* [test](dotnet-test.md)
* [publish](dotnet-publish.md)
* [pack](dotnet-pack.md)

## How does it work?
There are three main things to each CLI command:

1. The driver
2. The command or, as we like to call it, the "verb"
3. Command arguments

### Driver
The driver is called `dotnet` and it is the first thing that comes in any CLI invocation. It has two 
main jobs:

1. Run IL .NET code
2. Execute a verb 

Other than this, the driver doesn't have any smarts. When it is in the "verb execution" (CLI) mode, 


## Extensibility
Of cour
