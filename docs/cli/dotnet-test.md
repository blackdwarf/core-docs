dotnet-test
================

## NAME

`dotnet-test` - runs unit test using the configured test runner

## SYNOPSIS

dotnet-test [--configuration]  
    [--parentProcessId] [--port]  
    [< project >]  

## DESCRIPTION

`dotnet-test` is used to execute unit tests in a given project. Unit tests are class library 
projects that have dependencies on the unit test framework (e.g. NUnit or xUnit) and the 
dotnet test runner for that unit testing framework. These are packaged as NuGet packages and are 
restored as ordinary dependencies for the project.

Test projects also need to specify a test runner property in project.json using the "testRunner" node. 
This value should contain the name of the unit test framework.

Below is a sample project.json that shows the needed properties:

```json
{
    "version": "1.0.0-*",

    "dependencies": {
        "NETStandard.Library": "1.0.0-*",
        "xunit": "2.1.0-*",
        "dotnet-test-xunit": "1.0.0-*"
    },
    "testRunner": "xunit",

    "frameworks": {
        "netstandard1.5": {
        }
    }
}
```

Running `dotnet-test` in a directory will default to using the project.json file in that directory. This can be overriden
using the <project> argument. 

`dotnet-test` is also used as a test runner for .NET Core applications in Visual Studio and can be used by other 
IDEs and editors as well. It implements a simple protocol that is activated when it is ran with the `--port` argument. 
This will make `dotnet test` listen on a port for communications from the calling process. Calling processes need to 
also specify their process ID with the `--parentProcessId` argument so that the test runner can exit when they exit. 

## OPTIONS

`[project]` 
    
Specifies a path to the test project. If omitted, will default to current directory. 

`-c`, `--configuration` [CONFIGURATION]

Configuration under which to build. Defaults to Release. 

--parentProcessId

Used by IDEs to specify their process ID. Test will exit if the parent process does.

`--port`

Used by IDEs to specify a port number to listen for a connection.

## EXAMPLES

`dotnet-test`

Run the tests in the project in the current directory. 

`dotnet-test /projects/test1/project.json`

Run the tests in the test1 project. 

