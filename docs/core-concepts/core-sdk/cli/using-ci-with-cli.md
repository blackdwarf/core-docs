# Using .NET Core CLI tools in Continuous Integration

## Overview
This document outlines the 

In general, on a CI build server, you want to automate the installation through some way. The automation, ideally, should 
not require administrative privileges if at all possible. 

For SaaS CI solutions, there are several options. This document will cover two very popular ones, [TravisCI]() and 
[AppVeyor](). There are, of course, many other services out there, but the installation mechanisms should be similar.

## Installation options for CI build servers

## Using the native installers
If requiring administrative privileges is not a drawback, native installers (DEB and RPM packages) can be used to set up
 the build server. This approach has one advantage which is that it covers installing native dependencies on each platform 
needed for 

## Using the installer script
Using the installer script allows for non-administrative 

The installation script reference can be found in the [dotnet-install](dotnet-install-script.md) document. 

### Dealing with the dependencies
Using the installer script means that the native dependencies are not installed automatically and that you have to 
install them if the underlying operating system on the machine doesn't have them.

You can see the list of pre-requisites in the [CLI repo](). 

## CI services setup examples
The below sections show examples of configurations using the mentioned CI SaaS offerings. 

### TravisCI

### AppVeyor

## CI build server examples

### Jenkins


