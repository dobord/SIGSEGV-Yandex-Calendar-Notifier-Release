# SIGSEGV Yandex Calendar Notifier Releases

![SIGSEGV Yandex Calendar Notifier icon](assets/icon.png)

This public repository is updated automatically from
[`dobord/SIGSEGV-Yandex-Calendar-Notifier`](https://github.com/dobord/SIGSEGV-Yandex-Calendar-Notifier).
It contains user-facing documentation and the latest published Windows release package set.

## Description

SIGSEGV Yandex Calendar Notifier is a Windows 10/11 application that synchronizes selected Yandex calendars through
CalDAV, publishes upcoming events into the signed-in user's Windows Calendar, and shows Windows toast reminders.

The product has two installed parts:

- `ycn_config_app.exe`: the Qt configuration app for account settings, calendar selection, manual sync, and diagnostics.
- `ycn_calendar_bridge.exe`: the packaged per-user background agent that writes to Windows Calendar and sends reminders.

## Release Files

Download all files from the `release/` directory. The MSI/ZIP and MSIX/CER files must have the same package version;
mixing versions can leave the configuration app talking to an older packaged bridge.

- `SIGSEGVYandexCalendarNotifier-<version>-Windows-AMD64.msi`: Windows installer for the configuration app.
- `SIGSEGVYandexCalendarNotifier-<version>-Windows-AMD64.zip`: portable archive for the configuration app.
- `SIGSEGVYandexCalendarBridge-<version>.0-x64.msix`: packaged Windows Calendar bridge.
- `SIGSEGVYandexCalendarBridge-<version>.0-x64.cer`: public signing certificate for the MSIX.

## Installation

1. Install the configuration app from the MSI, or unpack the ZIP into a directory owned by the current user.
2. Trust the MSIX certificate from an elevated PowerShell session if the release uses a self-signed certificate:

```powershell
Import-Certificate `
  -FilePath .\SIGSEGVYandexCalendarBridge-<version>.0-x64.cer `
  -CertStoreLocation Cert:\LocalMachine\Root
```

3. Install the MSIX bridge for the signed-in user:

```powershell
Add-AppxPackage -Path .\SIGSEGVYandexCalendarBridge-<version>.0-x64.msix
```

4. Verify that the packaged bridge can access Windows Calendar:

```powershell
ycn_calendar_bridge.exe --probe
```

If App Installer reports `0x800B0109` or `0x800B010A`, Windows does not trust the signing chain yet. Re-import the
matching `.cer` into `Cert:\LocalMachine\Root` from an elevated PowerShell session, then retry the MSIX installation.

## Documentation

- [User manual](docs/user_manual.md)
- [Windows Calendar bridge notes](docs/windows_calendar_bridge.md)
- [Project specification](docs/project_specification.md)

## Publishing Model

This repository publishes GitHub Releases and tags that match the source repository release tags. Release assets uploaded
by automation are limited to the installable package files: `.msi`, `.zip`, `.msix`, and `.cer`.

## Source

Development, issues, and CI configuration live in the source repository:

https://github.com/dobord/SIGSEGV-Yandex-Calendar-Notifier
