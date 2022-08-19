---
title: "Unreal Engine 5: The Engine"
description: "Discussion of Unreal Engine 5, including how to use a Custom Engine that you can modify yourself."
breadcrumb_path: "UE5"
breadcrumb_name: "Engine"
---


# Unreal Engine 5: Using the Engine


## How to Generate Project Files

No matter your platform or version, you'll need to Generate Project Files for your project
in order to be able to build it.

In these examples, I'm using PowerShell 7.  If you use another shell, you can still figure out
what I'm doing here.


## Custom Engine Command: `-switchversionsilent`

If you're running a Custom Engine, you should generate your project files using
the `switchversionsilent` command.

```powershell
####  Assign $EngineRoot as $UProjectFile's custom engine (if it isn't already),
####  then Generate Visual Studio project files...

& $UnrealVersionSelector -switchversionsilent $UProjectFile $EngineRoot
```

See [How to Compute these PowerShell Variables](#How_to_Compute_these_PowerShell_Variables)
below for specifics.  In particular, `$EngineRoot` must be the `Engine` PARENT directory,
or the `Root` directory in which the `Engine` directory exists.

## Launcher Engine Command: `-projectfiles`

When using an Epic Games Launcher Engine, use the `-projectfiles` command instead.

In this case, the Engine will continue to be managed by the Epic Games Launcher tools.
Thus the command is simpler, you only need to tell it the name of your `$UProjectFile`.

```powershell
####  Use the Epic Games Launcher to: Generate Visual Studio project files...
& $UnrealVersionSelector -projectfiles $UProjectFile
```

<a id="How_to_Compute_these_PowerShell_Variables"></a>
## How to Compute these PowerShell Variables

These variables are used both for Custom Engines and for Launcher Engines.

In both cases we need to know the path to `UnrealVersionSelector` on your system.

- You need to compute these variables for your system:

```powershell
# Path to your .uproject (can be relative)
$UProjectFile = "D:/Dev/UE5/XistGame/XistGame.uproject"

#-- For Launcher Engines, you need to find this, it will be like: "C:\Program Files\Epic Games\Launcher"
#-- For Custom Engines, wherever you cloned the UnrealEngine repo
#-- Value as required by UnrealVersionSelector: parent of "D:/Dev/UE5/Engine" is the "root"
$EngineRoot = "D:/Dev/UE5"  ##  e.g. "D:/Dev/UE5"

##  e.g. D:/Dev/UE5/Engine/Binaries/Win64/UnrealVersionSelector.exe
$UnrealVersionSelector = $EngineBin + "/Engine/Binaries/Win64/UnrealVersionSelector.exe"
```

In the example calculation of `$EngineRoot` above, I've cloned the UnrealEngine source into `D:/Dev/UE5`.

See the discussion below for more info on the expected directory structure for custom engines.


## Custom Engine Directory Structure

Assume you checkout the
[UnrealEngine Github repository](https://github.com/EpicGames/UnrealEngine)
to `D:\Dev\UE5` (Windows) or `/Dev/UE5` (Linux/Mac).

```powershell
git clone https://github.com/EpicGames/UnrealEngine /Dev/UE5
```

You'll end up with a directory structure that looks something like this:

```text
Dev
└── UE5
    └── Engine
        ├── Binaries
        ├── ...
        └── Source
```

## Suggested Project Directory Structure

Now assume you have your own game project, `XistGame`.

You may want to put it inside the `/Dev/UE5` directory, like this:

```text
Dev
└── UE5
    ├── Engine
    └── XistGame
        ├── Content
        ├── Source
        ├── ...
        └── XistGame.uproject
```

In this way, your `XistGame` project will share the same parent directory as the UE5 `Engine` directory.

Though it is not strictly required to put your project into this hierarchy, it does make it easier
to automate work flows, for example.  You can very easily (as I have) automate build tools to
auto-select the engine from the relative project location `../Engine/` which reduces configuration
management overhead.

That being said, the Engine works relative to your `.uproject` and so it doesn't actually care
where you put your files.

Ultimately, you can put your project anywhere you want.


# Building a Custom UE5 Engine


Before you download the UE5 source, make sure you are familiar with this material:

[Official UE 5 Docs: Downloading Unreal Engine Source Code](https://docs.unrealengine.com/5.0/en-US/downloading-unreal-engine-source-code/)

Unless you know what you are doing, you will probably want the `release` branch, which is the most
recent official release of the engine.


## Windows: Install .NET v4.5 for VS 2022

<tip>UE5 requires .NET v4.5 and will NOT work with v4.8</tip>

<problem>
YOU DO NOT WANT TO USE .NET v4.8.
</problem>

If you are using Visual Studio 2022, you will likely need to manually download
and install .NET v4.5 framework, which is required by UE5 as of 5.0.3, but is
no longer supported by Microsoft.

[How to install .NET v4.5 on Windows](https://thomaslevesque.com/2021/11/12/building-a-project-that-target-net-45-in-visual-studio-2022/)

You'll want to make sure .NET v4.5 is installed BEFORE you try to build the engine.
You will know that you need this if Visual Studio 2022 gives you a warning that .NET v4.5
is missing.  It will ask if you want to upgrade the project to use v4.8.

If you try to compile with v4.8, it will compile mostly OK, and
then your engine/game will crash a lot in places it has no business crashing.

**UE5 requires .NET v4.5.** Install it via the methodology described at the above link.