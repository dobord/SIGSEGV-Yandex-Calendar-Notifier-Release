# Project Specification: SIGSEGV Yandex Calendar Notifier

## Overview

This project provides a Windows 10/11 per-user background agent and Qt configuration application that synchronize a
user's Yandex calendars through CalDAV, expand recurring events into concrete upcoming occurrences, mirror them into
the user's Windows Calendar, and show toast notifications before meetings.

## Components

| Component | Responsibility |
|---|---|
| **Packaged per-user agent** | `ycn_calendar_bridge.exe` runs with MSIX package identity. It polls CalDAV, expands recurring events, updates the per-user local store, publishes to `AppointmentStore`, and shows toast notifications. |
| **Qt configuration app** | `ycn_config_app.exe` discovers calendars, edits settings, saves the DPAPI-protected config, and talks to the running agent over local IPC. Release GUI builds use the Windows GUI subsystem and do not open a console window. |
| **Local store** | Per-user cache under `%LOCALAPPDATA%\SIGSEGVYandexCalendarNotifier`. It stores calendars, events, reminder overrides, and Windows Calendar publication state. |
| **Credential storage** | The Yandex app password is persisted as `app_password_dpapi`, encrypted with Windows DPAPI for the current user. |
| **IPC layer** | Local named-pipe API used by the GUI and CLI helper commands to apply config, force sync, list events, publish, and verify. |
| **Tests** | C++ unit/component tests plus CMake/PowerShell checks for packaging, MSIX capabilities, DPAPI, and toast notification paths. |

## Data Flow

1. The user configures the Yandex account and selected calendars in the Qt app.
2. The config is saved to `%LOCALAPPDATA%\SIGSEGVYandexCalendarNotifier\config.json`; new writes encrypt the app
   password with DPAPI.
3. The packaged agent runs for the signed-in user, reads the config, and periodically fetches Yandex CalDAV data.
4. The agent updates the local event store, expands recurring CalDAV events for the publication horizon, and publishes
   enabled upcoming occurrences into an app-owned Windows `AppointmentCalendar`.
5. The agent dispatches due reminders through Windows toast notifications.
6. The GUI and CLI commands use IPC to inspect or trigger the running agent.

## Packaging

The Qt configuration app and Windows Calendar writer are distributed together in one MSIX package. The Windows Calendar
writer must be MSIX-packaged because `AppointmentStore` requires package identity and the `appointmentsSystem`
capability.

The project no longer installs a machine-wide background process and no longer writes shared operational state to a
machine-wide data directory.

## User Commands

```powershell
ycn_calendar_bridge.exe --install
ycn_calendar_bridge.exe --run
ycn_calendar_bridge.exe --uninstall
ycn_calendar_bridge.exe --sync-and-publish
ycn_calendar_bridge.exe --verify
```

`--install` registers per-user logon startup through Task Scheduler when available and falls back to a Startup folder shortcut when task creation is denied for the current non-admin user. `--run` is the foreground agent entry point. `--uninstall` removes both startup registrations and cleans the legacy HKCU Run value.

## Future Work

- Two-way CalDAV synchronization.
- Rich toast activation that opens the related event in the configuration app.
- Richer recurrence diagnostics in the UI.
- Localization for Russian and English UI strings.
