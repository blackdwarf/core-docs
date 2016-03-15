# Migrating from DNX to .NET Core CLI

## Overview
With RC1 release of .NET Core and ASP.NET Core 1.0, we introduced

As a slight refresher, let's recap what DNX was about. DNX was a runtime and a toolset used to build .NET Core and, 
more specifically, ASP.NET Core 1.0 applications. It consisted of 3 main pieces:

1. DNVM - an install script to obtaining new DNX-es
2. DNX (Dotnet Execution Runtime) - the runtime that executes your code
3. DNU (Dotnet Developer Utility) - tooling for managing dependencies, building and publishing your apps

With the introduction of the CLI, all of the above are part of a single toolset now, and thus DNX has been 
effectively discontinued. However, since it was available for a long time, you might have projects that you 
built using it that you would want to move off to the new CLI tooling. 

This migration guide will cover the essentials on how to migrate projects off of DNX and to .NET Core CLI. If you are just 
starting a project on .NET Core from scratch, you can freely skip this document. 

## Main changes in the tooling
There are some general changes in the tooling that should be outlined first. 

### No more DNVM
DNVM, short for *DotNet Version Manager* was a shell/PowerShell script used to install a DNX on your machine. It helped
users get the DNX they need from the feed they specified (or default ones) as well as mark a certain DNX "active", which 
would put it on the $PATH for the given session. This would allow you to use the various tools.

DNVM will be discontinued because its feature set is made redundant by changes coming in the CLI.

CLI comes packaged in two main ways, as was specified in the [intro document](overview.md#installation):

1. Native installers for a given platform
2. Install script for other situations (like CI server)

Given this, the DNVM install features are not needed. What about the runtime selection features? 

You reference a runtime in your `project.json` by adding a package of a certain version to your dependencies. With this change
your application will be able to use the new runtime bits. Getting these bits to your machine is the same as with the CLI: 
you install the shared runtime via one of the native installers it supports or via its install script. 

### Different commands
If you were using DNX, you used some commands from one of its three parts (DNX, DNU or DNVM). With the CLI, some of these
commands change, some are not available and some are same but have slightly different semantics. 

The table below shows the mapping between the DNX/DNU commands and their CLI counterparts.


| DNX command                    	| CLI command    	| Description                                                                                                     	|
|--------------------------------	|----------------	|-----------------------------------------------------------------------------------------------------------------	|
| dnx run                        	| dotnet run     	| Run code from source.                                                                                           	|
| dnu build                      	| dotnet build   	| Build an IL binary of your code.                                                                                	|
| dnu pack                       	| dotnet pack    	| Package up a NuGet package of your code.                                                                        	|
| dnx [command] (e.g. "dnx web") 	| N/A\*          	| In DNX world, run a command as defined in the project.json.                                                     	|
| dnu install                    	| N/A\*          	| In the DNX world, install a package as a dependency.                                                            	|
| dnu restore                    	| dotnet restore 	| Restore dependencies specified in your project.json.                                                            	|
| dnu publish                    	| dotnet publish 	| Publish your application for deployment in one of the three form (portable, portable w/ native and standalone). 	|
| dnu wrap                       	| N/A\*          	| In DNX world, wrap a project.json in csproj.                                                                    	|
| dnu commands                   	| N/A\*          	| In DNX world, manage the globally installed commands.                                                           	|

(\*) - these features are not supported in the CLI by design. 

Using the CLI tooling is 

## Features that are not supported
As the table above shows, there are features from the DNX world that we decided not to support in the CLI, at least for 
the time being. This section will go through the most important ones and outline the rationale behind not supporting 
them as well as workarounds if you do need them.

### Global commands
DNU came with a concepts called "global commands". These were, essentially, console applications packaged up as NuGet 
packages with a shell script that would invoke the proper DNX to run the application. 

The CLI does not support this concept. It does, however, support the concept of adding per-project commands that can be 
invoked using the familiar `dotnet <command>` syntax. More about this can be found in the [extensibility overview](overview.md#extensibility). 

### Installing dependencies
As of v1, the CLI doesn't have the `install` command for installing dependencies. In order to install a package off of NuGet, you would 
need to add it as a dependency to your `project.json` file and then run `dotnet restore`. This is a point-in-time thing, 
as we will most likely be adding an install command to the toolset in the future in some way or form. 

## Migrating your DNX project to .NET Core CLI
In addition to using new commands when working with your code, there are two major thing left in migrating from DNX:

1. Migrating the project file (`project.json`) itself to the CLI tooling.
2. Migrating off of any DNX APIs to their BCL counterparts. 

### Migration the project file
The CLI and DNX both use the same basic project system based on `project.json` file. The syntax and the semantics of the 
project file are pretty much the same, with mild differences based on the scenarios. Let's see what those differences are. 

If you are building a console application, you need to add the following snippet to your project file:

```json
"compilationOptions": {
    "emitEntryPoint": true
}
```
This instructs `dotnet build` to emit an entry point for your application, effectively making your code runnable. If 
you are building a class library, simply omit the above section.

If you have a "commands" section in your `project.json`, you can remove it. For ASP.NET Core applications, you can use 
just `dotnet run` to run them (or `dotnet build` and then `dotnet <path-to-dll>`); with the CLI, the need for a special 
command "web" went away. 

Some of the commands that used to exist as DNU commands, such as Entity Framework CLI commands, are being ported to be 
per-project extensions to the CLI. If you built your own commands that you are using in your projects, you need to 
replace them with CLI extensions. In this case, the `commands` node in `project.json` needs to be replaced by the 
`tools` node and it needs to list the tools dependencies as explained in the 
[CLI extensibility section](overview.md#extensibility). 

After this, one thing that remains is to re-target your project to the shared runtime. This is done by introducing a
specific package dependency to your project.

```json
"dependencies": {
    "Microsoft.NETCore.App": "1.0.0-<version>"
}
```

After this, running `dotnet restore` will restore the dependencies of your project. Running `dotnet build` will show 
any build errors, though there shouldn't be too much of them. After your code is building and running properly, you 
are good to go! Congratulations, the migration from DNX projects in now complete!

