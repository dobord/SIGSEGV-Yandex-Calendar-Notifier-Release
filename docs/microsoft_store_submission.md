# Microsoft Store Submission Checklist

This document tracks the repository-side work needed before submitting SIGSEGV Yandex Calendar Notifier to Microsoft
Store through Partner Center.

Official references:

- [Get started with Microsoft Store](https://learn.microsoft.com/en-us/windows/apps/publish/get-started)
- [App package requirements for MSIX apps](https://learn.microsoft.com/en-us/windows/apps/publish/publish-your-app/msix/app-package-requirements)
- [Upload MSIX app packages](https://learn.microsoft.com/en-us/windows/apps/publish/publish-your-app/msix/upload-app-packages)
- [Microsoft Store policies](https://learn.microsoft.com/en-us/windows/apps/publish/store-policies)
- [App capability declarations](https://learn.microsoft.com/en-us/windows/uwp/packaging/app-capability-declarations)

## Current Store Readiness

The app is already packaged as MSIX and targets `Windows.Desktop`. The package runs the Qt configuration app and the
per-user bridge as full-trust packaged desktop apps because Windows Calendar integration requires package identity and
the app also needs native Win32 behavior for the background agent.

The package declares:

- `runFullTrust` - required for the packaged Qt/Win32 desktop app and bridge process.
- `appointmentsSystem` - required for reading and writing the user's Windows Calendar through Windows calendar APIs.

The app stores configuration per Windows user and protects the Yandex app password with Windows DPAPI. It does not ship
a machine-wide service.

## Partner Center Values Required Before Upload

Partner Center assigns identity values that must match the MSIX manifest exactly. Copy these values from **Product
management -> Product identity**:

- `Package/Identity/Name`
- `Package/Identity/Publisher`
- `Package/Properties/PublisherDisplayName`

Use them when building a Store candidate package:

```powershell
.\scripts\build-msix-bridge.ps1 `
  -BuildBinDir .\build\windows-msvc2022\Release `
  -OutputDir artifacts\store `
  -Version 0.7.0.0 `
  -PackageIdentityName '<Partner Center Package/Identity/Name>' `
  -PackagePublisher '<Partner Center Package/Identity/Publisher>' `
  -PackagePublisherDisplayName '<Partner Center PublisherDisplayName>'
```

For the public GitHub release build, the defaults remain the current self-signed/sideload identity.

## Privacy Policy

Microsoft Store policy requires a privacy policy for Desktop Bridge and Win32 products and for products that access
personal information. This app accesses calendar data, account email, and a user-provided Yandex app password, so the
privacy policy is mandatory.

Use this public URL in Partner Center:

```text
https://github.com/dobord/SIGSEGV-Yandex-Calendar-Notifier-Release/blob/main/docs/privacy_policy.md
```

## Restricted Capability Justification

Use this text in Partner Center if the submission asks for restricted capability justification:

```text
SIGSEGV Yandex Calendar Notifier is a packaged desktop app that synchronizes selected Yandex CalDAV calendars into the
signed-in user's Windows Calendar and shows local Windows toast reminders. The runFullTrust capability is required
because the app contains a Qt/Win32 configuration UI and a per-user native background bridge process that must run as a
packaged classic desktop app. The appointmentsSystem capability is required because the bridge publishes selected
calendar events into the user's Windows Calendar through Windows AppointmentStore APIs. The app does not install a
machine-wide service and stores configuration per Windows user with the Yandex app password protected by Windows DPAPI.
```

## Notes for Certification

If certification needs a test account, provide a working Yandex test account and app password in **Notes for
certification**. The tester should be able to open the app, save the account settings, discover calendars, run
`Sync + publish`, and run `Verify Windows Calendar`.

Suggested certification notes:

```text
This app requires a Yandex Calendar account with CalDAV enabled. Use the test credentials below only for Microsoft Store
certification. After installation, open SIGSEGV Yandex Calendar Notifier, enter the CalDAV URL, email, and app password
in Account, click Save locally, install/start the agent from Status, discover calendars from Calendars, then run
Sync + publish and Verify Windows Calendar from Events.
```

## Store Listing Draft

Short description:

```text
Synchronize selected Yandex Calendar events into Windows Calendar and show local Windows toast reminders.
```

Description:

```text
SIGSEGV Yandex Calendar Notifier is a Windows app for people who use Yandex Calendar but want their upcoming events
available in Windows Calendar and local Windows notifications.

The app connects to Yandex Calendar through CalDAV, lets you choose which calendars to synchronize, publishes upcoming
events into Windows Calendar, and runs a per-user background agent for periodic sync and reminders.

Your configuration stays on the current Windows profile. The Yandex app password is protected locally with Windows
DPAPI. The app does not upload calendar data to a SIGSEGV cloud service.
```

Keywords:

```text
yandex calendar, caldav, windows calendar, reminders, calendar sync
```

Russian short description:

```text
Синхронизирует выбранные события Yandex Calendar в Windows Calendar и показывает локальные Windows-напоминания.
```

Russian description:

```text
SIGSEGV Yandex Calendar Notifier - приложение Windows для пользователей Yandex Calendar, которым нужны события в
Windows Calendar и локальные уведомления Windows.

Приложение подключается к Yandex Calendar через CalDAV, позволяет выбрать календари для синхронизации, публикует
ближайшие события в Windows Calendar и запускает фоновый агент текущего пользователя для периодической синхронизации и
напоминаний.

Конфигурация остается в текущем профиле Windows. Пароль приложения Yandex защищается локально через Windows DPAPI.
Приложение не загружает календарные данные в облачный сервис SIGSEGV.
```

Russian keywords:

```text
yandex calendar, caldav, windows calendar, напоминания, синхронизация календаря
```

Suggested category: Productivity.

Suggested age rating: Complete the IARC questionnaire in Partner Center. The app has no user-generated content, no
commerce, no ads, and is not directed at children.

## Pre-Submission Validation

Before uploading to Partner Center:

1. Build the Release configuration with Qt enabled.
2. Build a Store candidate MSIX with Partner Center identity overrides.
3. Install the candidate locally in a clean Windows user profile.
4. Run `ycn_calendar_bridge.exe --probe` from the installed package.
5. Run `Sync + publish` and confirm that events appear in Windows Calendar.
6. Run the Windows App Certification Kit if available on the release machine.
