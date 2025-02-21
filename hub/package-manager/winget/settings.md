---
title: settings command
description: Provides customizations for the Windows Package Manager.
ms.date: 08/02/2022
ms.topic: article
ms.localizationpriority: medium
---

# settings command (winget)

The **settings** command of the [winget](./index.md) tool allows you to customize your Windows Package Manager client experience. You can change defaults and try out experimental features that are enabled in your client.

The **settings** command will launch your default text editor. Windows by default will launch Notepad as an option.  We recommend using a tool like [Visual Studio code](https://code.visualstudio.com/).  

>[!NOTE]
>You can easily install Visual Studio Code by typing `winget install Microsoft.VisualStudioCode`

## Use the winget settings command

Launch your default JSON editing tool: `winget settings`

When you launch the settings for the first time, there will be no settings specified. At the top of the JSON file we provide a [link](https://aka.ms/winget-settings) where you can discover the latest experimental features and settings.

The code snippet below is an example of what your settings file should look like if you would like to enable or modify some of these experimental features and settings.

```json
{
    "$schema": "https://aka.ms/winget-settings.schema.json",

    // For documentation on these settings, see: https://aka.ms/winget-settings
    "experimentalFeatures": {
	  "dependencies": true,
	  "directMSI": false,
	  "zipInstall": false,
    },
    "visual": {
        "progressBar": "rainbow"
    },
    "source": {
        "autoUpdateIntervalInMinutes": 5
    },
}
```

We have also defined a schema for the settings file. This allows you to use TAB to discover settings and syntax if your JSON editor supports JSON schemas.

> [!NOTE]
> Experimental features are only available in preview builds. Instructions for obtaining a preview build can be found in the [GitHub repository](https://github.com/microsoft/winget-cli).

## Updating settings

The following settings are available for the 1.0 release of the Windows Package Manager.

### source settings

The `source` settings involve configuration to the WinGet source.

```json
"source": {
    "autoUpdateIntervalInMinutes": 3
},
```

#### autoUpdateIntervalInMinutes

A positive integer represents the update interval in minutes. The check for updates only happens when a source is used. A zero will disable the check for updates to a source. Any other values are invalid.

- Disable: 0
- Default: 5

To manually update the source use `winget source update`.

### visual settings

The `visual` settings involve visual elements that are displayed by WinGet

```json
"visual": {
    "progressBar": "accent"
},
```

#### progressBar

Color of the progress bar that WinGet displays when not specified by arguments. 

- accent (default)
- retro
- rainbow

### installBehavior settings

The `installBehavior` settings affect the default behavior of installing and upgrading (where applicable) packages.

### disableInstallNotes
The `disableInstallNotes` behavior affects whether installation notes are shown after a successful install. Defaults to `false` if value is not set or is invalid.

```json
    "installBehavior": {
        "disableInstallNotes": true
    },
```

### portablePackageUserRoot setting

The `portablePackageUserRoot` setting affects the default root directory where packages are installed to under `User` scope. This setting only applies to packages with the `portable` installer type. Defaults to `%LOCALAPPDATA%/Microsoft/WinGet/Packages/` if value is not set or is invalid.

> Note: This setting value must be an absolute path.

```json
    "installBehavior": {
        "portablePackageUserRoot": "C:/Users/FooBar/Packages"
    },
```

### portablePackageMachineRoot setting

The `portablePackageMachineRoot` setting affects the default root directory where packages are installed to under `Machine` scope. This setting only applies to packages with the `portable` installer type. Defaults to `%PROGRAMFILES%/WinGet/Packages/` if value is not set or is invalid.

> Note: This setting value must be an absolute path.

```json
    "installBehavior": {
        "portablePackageMachineRoot": "C:/Program Files/Packages/Portable"
    },
```

### preferences and requirements settings

Some of the settings are duplicated under `preferences` and `requirements`. 

- The `preferences` setting affects how the various available options are sorted when choosing the one to act on. For example, the default scope of package installs is for the current user, but if that is not an option then a machine level installer will be chosen.
- The `requirements` setting filters the options, potentially resulting in an empty list and a failure to install. In the previous example, a user scope requirement would result in no applicable installers and an error.

Any arguments passed on the command line will effectively override the matching `requirement` setting for the duration of that command.

#### scope

The `scope` behavior affects the choice between installing a package for the current user or for the entire machine. The matching parameter is `--scope`, and uses the same values (`user` or `machine`). See  [known issues relating to package installation scope](./troubleshooting.md#scope-for-specific-user-vs-machine-wide).

```json
"installBehavior": {
    "preferences": {
        "scope": "user"
    }
},
```

#### locale

The `locale` behavior affects the choice of installer based on installer locale. The matching parameter is `--locale`, and uses bcp47 language tag.

```json
"installBehavior": {
    "preferences": {
        "locale": [ "en-US", "fr-FR" ]
    }
},
```

#### architectures

The `architectures` behavior affects what architectures will be selected when installing a package. The matching parameter is `--architecture`. Note that only architectures compatible with your system can be selected.

```json
    "installBehavior": {
        "preferences": {
            "architectures": ["x64", "arm64"]
        }
    },
```

### uninstallBehavior

The `uninstallBehavior` settings affect the default behavior of uninstalling (where applicable) packages.

### purgePortablePackage

The `purgePortablePackage` behavior affects the default behavior for uninstalling a portable package. If set to `true`, uninstall will remove all files and directories relevant to the `portable` package. This setting only applies to packages with the `portable` installer type. Defaults to `false` if value is not set or is invalid.

```json
    "uninstallBehavior": {
        "purgePortablePackage": true
    },
```

### telemetry settings

The `telemetry` settings control whether winget writes ETW events that may be sent to Microsoft on a default installation of Windows.

See [details on telemetry](https://github.com/microsoft/winget-cli/blob/master/README.md#datatelemetry), and our [primary privacy statement](https://github.com/microsoft/winget-cli/blob/master/privacy.md).

#### disable

```json
"telemetry": {
    "disable": true
},
```

If set to true, the `telemetry.disable` setting will prevent any event from being written by the program.

### network settings

The `network` settings influence how winget uses the network to retrieve packages and metadata.

#### downloader

The `downloader` setting controls which code is used when downloading packages. The default is `default`, which may be any of the options based on our determination.

`wininet` uses the [WinINet](/windows/win32/wininet/about-wininet) APIs, while `do` uses the [Delivery Optimization](https://support.microsoft.com/windows/delivery-optimization-in-windows-10-0656e53c-15f2-90de-a87a-a2172c94cf6d) service.

```json
"network": {
    "downloader": "do"
}
```

### logging settings

The `logging` settings control the level of detail in log files. `--verbose-logs` will override this setting and always creates a verbose log.


```json
"logging": {
    "level": "verbose"
}
```

#### level

The following logging levels are available. Defaults to `info` if the value is not set or is invalid. 

- verbose
- info
- warning
- error
- critical

## Enabling experimental features

To discover which experimental features are available, go to [https://aka.ms/winget-settings](https://aka.ms/winget-settings) where you can see the experimental features available to you.
