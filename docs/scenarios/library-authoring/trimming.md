---
title: How to trim your package dependencies
description: How to trim your package dependencies
keywords: .NET, .NET Core
author: BillWagner
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 916251e3-87f9-4eee-81ec-94076215e6fa
---

# How to trim your package dependencies

By [Phillip Carter](https://github.com/cartermp)

This article covers what you need to know about trimming your package dependencies to use only the packages you need for .NET Core RC2.

## What trimming means

.NET Core RC2 introduces the [.NET Standard Library metapackage](https://www.nuget.org/packages/NETStandard.Library/1.5.0-rc2-24027), which is a NuGet package composed of other packages.  It is curated to be appropriate for developing libraries.  However, there's a good chance that your library won't use every single package it contains.  When authoring a library and distributing it over NuGet, it's a best practice to "trim" your dependencies down to only the packages you actually use.  This results in a smaller overall footprint for NuGet packages.

## How to trim

Currently, there is no official tooling which supports package trimming.  You'll have to do it manually.  The general process looks like the following:

1. Reference `NETStandard.Library` version `1.5.0-rc2-24027` in a `dependencies` section of your `project.json`.
2. Restore packages with `dotnet restore` from the command line.
3. Inspect the `project.lock.json` file and find the `NETSTandard.Library` section.  It's near the beginning of the file.
4. Copy all of the listed packages under `dependencies`.
5. Remove the `.NETStandard.Library` reference and replace it with the copied packages.
6. Remove references to packages you don't need.

You can find out which packages you don't need by one of the following ways:

1. Trial and error.  This involves removing a package, restoring, seeing if your library still compiles, and repeating this process.
2. Using a tool such as [ILSpy](http://ilspy.net) or [.NET Reflector](http://www.red-gate.com/products/dotnet-development/reflector) to peek at references to see what your code is actually using.  You can then remove packages which don't correspond to types you're using.

## Example 

Imagine that you wrote a library which provided additional functionality to generic and concurrent collection types.  Such a library would need to depend on packages such as `System.Collections`, but may not at all depend on packages such as `System.Net.Http`.  As such, it would be good to trim package dependencies down to only what this library required!

To trim this library, you start with the `project.json` file and add a reference to `NETStandard.Library` version `1.5.0-rc2-24027`.

```json
{
    "version":"1.0.0",
    "dependencies":{
        "NETStandard.Library":"1.5.0-rc2-24027"
    },
    "frameworks": {
        "netstandard1.0": {}
     }
}
```

Next, you restore packages with `dotnet restore`, inspect the `project.lock.json` file, and find all the packages restored for `NETSTandard.Library`.

Here's what the relevant section in the `project.lock.json` file looks like when targeting `netstandard1.0`:

```json
"NETStandard.Library/1.5.0-rc2-24027":{
    "type": "package",
    "dependencies": {
        "Microsoft.NETCore.Platforms": "1.0.1-rc2-24027",
        "Microsoft.NETCore.Runtime": "1.0.2-rc2-24027",
        "System.Collections": "4.0.11-rc2-24027",
        "System.Diagnostics.Debug": "4.0.11-rc2-24027",
        "System.Diagnostics.Tools": "4.0.1-rc2-24027",
        "System.Globalization": "4.0.11-rc2-24027",
        "System.IO": "4.1.0-rc2-24027",
        "System.Linq": "4.1.0-rc2-24027",
        "System.Net.Primitives": "4.0.11-rc2-24027",
        "System.ObjectModel": "4.0.12-rc2-24027",
        "System.Reflection": "4.1.0-rc2-24027",
        "System.Reflection.Extensions": "4.0.1-rc2-24027",
        "System.Reflection.Primitives": "4.0.1-rc2-24027",
        "System.Resources.ResourceManager": "4.0.1-rc2-24027",
        "System.Runtime": "4.1.0-rc2-24027",
        "System.Runtime.Extensions": "4.1.0-rc2-24027",
        "System.Text.Encoding": "4.0.11-rc2-24027",
        "System.Text.Encoding.Extensions": "4.0.11-rc2-24027",
        "System.Text.RegularExpressions": "4.0.12-rc2-24027",
        "System.Threading": "4.0.11-rc2-24027",
        "System.Threading.Tasks": "4.0.11-rc2-24027",
        "System.Xml.ReaderWriter": "4.0.11-rc2-24027",
        "System.Xml.XDocument": "4.0.11-rc2-24027"
    }
}
```

Next, copy over the package references into the `dependencies` section of the library's `project.json` file, replacing the `NETStandard.Library` reference:

```json
{
    "version":"1.0.0",
    "dependencies":{
        "Microsoft.NETCore.Platforms": "1.0.1-rc2-24027",
        "Microsoft.NETCore.Runtime": "1.0.2-rc2-24027",
        "System.Collections": "4.0.11-rc2-24027",
        "System.Diagnostics.Debug": "4.0.11-rc2-24027",
        "System.Diagnostics.Tools": "4.0.1-rc2-24027",
        "System.Globalization": "4.0.11-rc2-24027",
        "System.IO": "4.1.0-rc2-24027",
        "System.Linq": "4.1.0-rc2-24027",
        "System.Net.Primitives": "4.0.11-rc2-24027",
        "System.ObjectModel": "4.0.12-rc2-24027",
        "System.Reflection": "4.1.0-rc2-24027",
        "System.Reflection.Extensions": "4.0.1-rc2-24027",
        "System.Reflection.Primitives": "4.0.1-rc2-24027",
        "System.Resources.ResourceManager": "4.0.1-rc2-24027",
        "System.Runtime": "4.1.0-rc2-24027",
        "System.Runtime.Extensions": "4.1.0-rc2-24027",
        "System.Text.Encoding": "4.0.11-rc2-24027",
        "System.Text.Encoding.Extensions": "4.0.11-rc2-24027",
        "System.Text.RegularExpressions": "4.0.12-rc2-24027",
        "System.Threading": "4.0.11-rc2-24027",
        "System.Threading.Tasks": "4.0.11-rc2-24027",
        "System.Xml.ReaderWriter": "4.0.11-rc2-24027",
        "System.Xml.XDocument": "4.0.11-rc2-24027"
    },
    "frameworks":{
        "netstandard1.0": {}
    }
}
```

That's quite a lot of packages, many of which which certainly aren't necessary for extending collection types.  You can either remove packages manually or use a tool such as [ILSpy](http://ilspy.net) or [.NET Reflector](http://www.red-gate.com/products/dotnet-development/reflector) to identify which packages your code actually uses.

Here's what a trimmed package could look like:

```json
{
    "dependencies":{
        "Microsoft.NETCore.Platforms": "1.0.1-rc2-24027",
        "Microsoft.NETCore.Runtime": "1.0.2-rc2-24027",
        "System.Collections": "4.0.11-rc2-24027",
        "System.Collections.Concurrent": "4.0.12-rc2-24027",
        "System.Linq": "4.1.0-rc2-24027",
        "System.Runtime": "4.1.0-rc2-24027",
        "System.Runtime.Extensions": "4.1.0-rc2-24027",
        "System.Runtime.Handles": "4.0.1-rc2-24027",
        "System.Runtime.InteropServices": "4.1.0-rc2-24027",
        "System.Runtime.InteropServices.RuntimeInformation": "4.0.0-rc2-24027",
        "System.Threading.Tasks": "4.0.11-rc2-24027"
    },
    "frameworks":{
        "netstandard1.0": {}
     }
}
```

Now, it has a smaller footprint than if it had depended on the `NETStandard.Library` metapackage.
