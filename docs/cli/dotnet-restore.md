🔧 dotnet-restore
=================

## NAME

`dotnet-restore` - restores the dependencies and tools of the project being run on

## SYNOPSIS

dotnet-publish [--source]  
    [--packages] [--disable-parallel]  
    [--fallbacksource] [--configfile] [--verbosity]
    [< root >]  

## DESCRIPTION

`dotnet-restore` will use NuGet to restore dependencies as well as project-specific 
tools for a given project or set of projects. 

The NuGet.config resolution is 

## OPTIONS

`[root]` 
    
     A list of projects or project folders to restore. The list can contain either a path to a `project.json` file, path to `global.json` file or  folder. The restore operation will run recursivelly for all sub-directories and restore for each given project.json file it finds.

`-s`, `--source` [SOURCE    ]

    Specify a source to use during the restore operation. This will override all of the sources specified in the NuGet.config file(s). 


`-r`, `--runtime` [RID]

    Publish the application for a given runtime. If the option is not specified, the command will default to the runtime for the current operationg system. Supported values for the option at this time are:

        * ubuntu.14.04-x64
        * win7-x64
        * osx.10.10-x64

`-o`, `--output`

    Specify the path where to place the directory. If not specified, will default to _./bin/[configuration]/[framework]/[runtime]/_

`-c`, `--configuration [Debug|Release]`

    Configuration to use when publishing. If not specified, will default to "Debug".

## EXAMPLES

`dotnet-publish`

    Publish the current application using the `project.json` framework and runtime for the current operating system. 

`dotnet-publish ~/projects/app1/project.json`
    
    Publish the application using the specified `project.json`; also use framework specified withing and runtime for the current operating system. 
	
`dotnet-publish --framework dnxcore50`
    
    Publish the current application using the `dnxcore50` framework and runtime for the current operating system. 
	
`dotnet-publish --framework dnxcore50 --runtime osx.10.10-x64`
    
    Publish the current application using the `dnxcore50` framework and runtime for `OS X 10.10`

