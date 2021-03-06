# IISLogManager

[![CodeQL](https://github.com/rbleattler/IISLogManager/actions/workflows/codeql-analysis.yml/badge.svg)](https://github.com/rbleattler/IISLogManager/actions/workflows/codeql-analysis.yml)
[![pages-build-deployment](https://github.com/rbleattler/IISLogManager/actions/workflows/pages/pages-build-deployment/badge.svg)](https://github.com/rbleattler/IISLogManager/actions/workflows/pages/pages-build-deployment)
[![.NET](https://github.com/rbleattler/IISLogManager/actions/workflows/dotnet.yml/badge.svg)](https://github.com/rbleattler/IISLogManager/actions/workflows/dotnet.yml)

IISLogManager was built to facilitate central logging of IIS log messages from any number of IIS servers and websites. The core components of the tool were originally built in Windows PowerShell to maintain compatibility across a number of different operating system versions. The PowerShell versions of the log Core will be available for the time being but may become deprecated at some point in the future.

><font color="dark red">**NOTE**</font> : This tool set is under active development. At this time there are likely many bugs.

## Compatibility Statement

Libraries associated with IISLogManager are built to target `.NET Standard 2.0.X`. Because of this, they can be used with .Net Framework or .Net Core

## Core

The Core library is the backbone of this project, and may be paired with any ingestion solution. 
This library facilitates importing and parsing IIS log files. The default output of the parser is `List<IISLogObject>`. 
The end goal is for this to support numerous forms of output. At the time of this update, it currently only supports 
output to **JSON**.

## CLI

The CLI is being developed using the dotnet 6.0.x sdk. When releases are made available I will try to make them self-contained, 
but they may contain the runtimes as side-by-side libraries. The interactive parts of the CLI are written using an ANSI library, 
and thus as of this time, do not work properly on older systems (Server 2012 R2 or older). For more information on this, check 
out this MS DevBlog. [24 Bit Color in Windows Consoles](https://devblogs.microsoft.com/commandline/24-bit-color-in-the-windows-console/)

## Usage

> **Note**: This section is under construction. As this readme was adapted from a previous iteration of this project, this may be out of date. It will likely be moved to it's own directory with other documentation at a later time.

### Basic use, parsing an individual log file

**C#**
---
```csharp
            List<IISLogObject> logs = new List<IISLogObject>();
            using (ParserEngine parser = new ParserEngine([filepath]))
            {
                while (parser.MissingRecords)
                {
                    logs = parser.ParseLog().ToList();
                }
            }
```

**PowerShell**
---
```powershell
            # ParsedLogs returns an object of type : [System.Collections.Generic.List[IISLogManager.IISLogObject]]
            $ParseEngine = [IISLogManager.ParseEngine]::new("C:\inetpub\logs\LogFiles\W3SVC0\ex220207.log")
            $ParsedLogs = $ParseEngine.ParseLog()
            $ParsedLogs
```

### Parsing all log files for a site

**C#**
---
```csharp
//WIP
```

**PowerShell**
-----

```powershell
    $Factory = [IISLogManager.SiteObjectFactory]::new()
    $IISController = [IISLogManager.IISController]::new()
    $TargetSite = $IISController.ServerManager.Sites.Where{ $PSItem.Id -eq 1 }[0] # Get a site by ID, where the ID is 1. 
    $Site = $Factory.BuildSite($TargetSite)
    $Site.ParseAllLogs()
    # Returns [System.Collections.Generic.List[IISLogManager.IISLogObject]]
    # $Site.Logs
    # Return as Json 
    # $Site.Logs | ForEach-Object { $PSItem.ToJson() }
```

### Adaptation Credit Statement

A portion of the codebase in this project was adapted from [Kabindas/IISLogParser (Github)](https://github.com/Kabindas/IISLogParser). While the core code already existed in PowerShell prior to discovering this project, adopting his codebase solved some problems with memory usage I was running into at the time, and was adopted with permission from the project's creator. If you find this project useful, please drop a star on that project as well to give thanks.
