# Privacy Policy

Effective date: 2026-07-01

SIGSEGV Yandex Calendar Notifier is a Windows app that synchronizes selected Yandex Calendar events into the signed-in
user's Windows Calendar and shows local Windows toast reminders.

## Data The App Uses

The app uses the following information only to provide calendar synchronization:

- Yandex CalDAV base URL.
- Yandex account email.
- Yandex app password entered by the user.
- Calendar collection URLs and display names.
- Calendar event details returned by Yandex CalDAV, such as event title, start and end time, location, description,
  recurrence data, and event identifiers.
- Local diagnostic messages shown in the app's Status section.

## How Data Is Stored

Configuration is stored locally for the current Windows user under:

```text
%LOCALAPPDATA%\SIGSEGVYandexCalendarNotifier\config.json
```

The Yandex app password is protected with Windows Data Protection API (Windows DPAPI) for the current Windows user. The
DPAPI-protected value cannot be decrypted from another Windows account.

The app does not operate a SIGSEGV server and does not upload calendar data to a SIGSEGV-controlled cloud service.

## How Data Is Transmitted

The app connects to the Yandex CalDAV endpoint configured by the user to read calendar collections and events. The app
publishes selected upcoming events into the current user's Windows Calendar by using Windows calendar APIs.

Calendar data may therefore be processed by:

- Yandex, as the CalDAV calendar provider selected by the user.
- Microsoft Windows, as the local Windows Calendar platform and notification provider.

## User Controls

The user controls which calendars are synchronized by enabling or disabling calendar rows in the app. The user can stop
background synchronization by uninstalling the agent from the Status section or by removing the app.

The user can delete the local configuration file from the path shown above to remove locally stored app settings.

## Data Disclosure

SIGSEGV Yandex Calendar Notifier does not sell personal information and does not share calendar data with advertising
networks. The app has no in-app advertising and no in-app purchases.

## Children

The app is not directed at children under 13.

## Changes

This policy may be updated when the app's data handling changes. The current version is published with the app's public
documentation.

## Contact

For privacy questions, use the source repository issue tracker:

https://github.com/dobord/SIGSEGV-Yandex-Calendar-Notifier/issues
