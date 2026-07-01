# Changelog

## 0.6.0 - 2026-06-28

- Fix bridge logon autostart registration so only one startup mechanism remains active for the signed-in user.
- Add a guarded bridge entry point that prevents duplicate `--run` agents during logon races.
- Add Windows CI coverage for headless tests and MSIX layout generation on hosted Windows runners.
- Add a Windows 10/11 VM smoke-test script for installed MSIX autostart validation.
