# User Manual

## Description

SIGSEGV Yandex Calendar Notifier is a Windows 10/11 application that synchronizes selected Yandex calendars through
CalDAV, publishes upcoming events into the signed-in user's Windows Calendar, and shows Windows toast reminders.

The product is installed as one MSIX package containing two executables:

- `ycn_config_app.exe`: the Qt configuration app for account settings, calendar selection, manual sync, and diagnostics.
- `ycn_calendar_bridge.exe`: the packaged per-user background agent that writes to Windows Calendar and sends reminders.

## Release Files

Download both files from the same release package set when the release uses a self-signed certificate.

- `SIGSEGVYandexCalendarNotifier-<version>.0-x64.msix`: packaged configuration app and Windows Calendar bridge.
- `SIGSEGVYandexCalendarNotifier-<version>.0-x64.cer`: public signing certificate for the MSIX.

## Installation

1. Trust the MSIX certificate from an elevated PowerShell session if the release uses a self-signed certificate:

```powershell
Import-Certificate `
  -FilePath .\SIGSEGVYandexCalendarNotifier-<version>.0-x64.cer `
  -CertStoreLocation Cert:\LocalMachine\Root
```

2. Install the MSIX for the signed-in user:

```powershell
Add-AppxPackage -Path .\SIGSEGVYandexCalendarNotifier-<version>.0-x64.msix
```

3. Verify that the packaged bridge can access Windows Calendar:

```powershell
ycn_calendar_bridge.exe --probe
```

If App Installer reports `0x800B0109` or `0x800B010A`, Windows does not trust the signing chain yet. Re-import the
matching `.cer` into `Cert:\LocalMachine\Root` from an elevated PowerShell session, then retry the MSIX installation.

## Configuration

Configuration is stored per Windows user under:

```text
%LOCALAPPDATA%\SIGSEGVYandexCalendarNotifier\config.json
```

Open `ycn_config_app.exe`, enter the Yandex CalDAV credentials, and use `Discover calendars` to load the available
calendar list. Select the calendars to synchronize, save the configuration, and start or install the bridge agent from
the app.

The Yandex app password is stored as `app_password_dpapi`, encrypted with Windows DPAPI for the current Windows user.
The encrypted value cannot be moved to a different Windows account.

## User Guide

For a screenshot-based walkthrough, see [User guide](user_guide.md). A Russian version is available as
[Руководство пользователя](user_guide_ru.md).

Use the Calendars tab to refresh calendar names and choose which calendars should be synchronized. Some Yandex CalDAV
accounts do not return a complete calendar-home collection; if calendars are already present in the configuration, the
app refreshes each known collection directly and replaces URL fallbacks with server display names.

Use the Events tab to inspect synchronized events, trigger `Sync + publish`, and run `Verify Windows Calendar`.
Recurring Yandex events are expanded before publication. The bridge supports common iCalendar recurrence fields,
including `RRULE`, `EXDATE`, `RDATE`, `RECURRENCE-ID`, and cancelled occurrences.

Install the background agent for the current Windows user:

```powershell
ycn_calendar_bridge.exe --install
```

Start the agent manually for diagnostics:

```powershell
ycn_calendar_bridge.exe --run
```

Remove the per-user startup registration:

```powershell
ycn_calendar_bridge.exe --uninstall
```

The configuration app checks that the installed MSIX bridge version matches the configuration app version. If versions
do not match, install the `.msix` and `.cer` from the same release.
