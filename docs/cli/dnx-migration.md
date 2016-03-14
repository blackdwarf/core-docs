# Migrating from DNX to .NET Core CLI

## Overview
With RC1 release of .NET Core and ASP.NET Core 1.0, we introduced

**TODO: put a slight referesher on what is the DNX and whatnot**

## The reason for change

## Main changes in the tooling

### No more DNVM
DNVM, short for *DotNet Version Manager* was a shell/powershell script used to install a DNX on your machine. It allowed 
several features, some of which 

### Different commands
**TODO: add stuff here on the need of it all**
As was explained in the [overview of the CLI](overview.md), 
The table below outlines the mapping between existing DNX/DNU commands and 

| DNX command                    	| CLI command    	|
|--------------------------------	|----------------	|
| dnx run                        	| dotnet run     	|
| dnu build                      	| dotnet build   	|
| dnu pack                       	| dotnet pack    	|
| dnx [command] (e.g. "dnx web") 	| N/A\*            	|
| dnu install                    	| N/A\*            	|
| dnu restore                    	| dotnet restore 	|
| dnu publish                    	| dotnet publish 	|
| dnu wrap                       	| N/A\*            	|
| dnu commands                   	| N/A\*            	|

(\*) - these features are not supported in the CLI by design. 

## Features that are not supported
As the table above shows, there are features from the DNX world that we decided not to support in the CLI, at least for 
the time being. This section will go through the most important ones and outline the rationale behind not supporting 
them as well as workarounds if you do need them.

### Global commands
TBD

### Installing dependencies
TBD

## Migrating your DNX project to .NET Core CLI
TBD

