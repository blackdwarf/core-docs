dotnet command
==============

## NAME

dotnet -- general driver for running the command-line commands

## SYNOPSIS

dotnet [--version] [--help] [--verbose] < command > [< args >]

## DESCRIPTION
dotnet is a generic driver for the CLI toolchain. Invoked on its own, it will give out brief usage instructions. 

Each specific feature is implemented as a command. In order to use the feature, it is specified after dotnet, i.e. `dotnet compile`. All of the arguments following the command are command's own arguments.  


## OPTIONS
`-v, --verbose`

Enable verbose output.

`--version`

Print out the version of the CLI tooling

`-h, --help`

Print out a short help and a list of current commands. 

## DOTNET COMMANDS

The following commands exist for dotnet.

* [dotnet-new](dotnet-new.md)
   * Initializes a sample .NET Core console application. 
* [dotnet-restore](dotnet-restore.md)
  * Restores the dependencies for a given application. 
* [dotnet-build](dotnet-build.md)
  * Compile the application to either an intermidiate language (IL) or to a native binary. 
* [dotnet-run](dotnet-run.md)
   * Runs the application from source.
* [dotnet-publish](dotnet-publish.md)
   * Publishes a flat directory that contains the application and its dependencies, including the runtime binaries. 
* [dotnet-test](dotnet-test.md)
   * Runs tests using a test runner specified in project.json.
* [dotnet-pack](dotnet-pack.md)
   * Create a NuGet package of your code

## EXAMPLES

`dotnew new`

    Initializes a sample .NET Core console application that can be compiled and ran.

`dotnet restore`

    Restores dependencies for a given application. 

`dotnet compile`

    Compiles the application in a given directory. 

## ENVIRONMENT 

`DOTNET_PACKAGES`

    The primary package cache. If not set, defaults to $HOME/.nuget/packages on Unix or %LOCALAPPDATA%\NuGet\Packages (TBD) on Windows.

`DOTNET_PACKAGES_CACHE`

    The secondary cache. This is used by shared hosters (such as Azure) to provide a cache of pre-downloaded common packages on a faster disk. If not set it is not used.

`DOTNET_SERVICING`

    Specifies the location of the servicing index to use by the shared host when loading the runtime. 

