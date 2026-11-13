# Code review and build fixes — Docugen 0.6.2

## Root cause addressed

The GitHub Actions workflow uploaded the already-compressed portable ZIP as an Actions artifact. GitHub then wrapped that file in its own artifact ZIP. Downloading and extracting the artifact therefore produced another ZIP instead of the application files, which looked like a broken/incomplete build.

The workflow now uploads the published application directory as the Actions artifact. The separate portable ZIP is still created and attached to GitHub Releases. The downloaded Actions artifact should now contain `DocumentGenerator.exe`, `e_sqlite3.dll`, and the companion files directly.

## Build-time safeguards added

- CI and local publish now fail if `DocumentGenerator.exe` is missing or suspiciously small.
- CI and local publish now fail if `e_sqlite3.dll` is missing. SQLite is required during startup, so this catches a package that would otherwise build but fail at runtime.
- Updated build instructions to explain the difference between the Actions artifact and the Release ZIP.

## Runtime/deployment note

This is a portable app and creates `data`, `templates`, `exports`, `backups`, `logs`, and `cache` beside the executable. Extract it to a writable folder (for example `C:\Apps\Docugen`), not `C:\Program Files`. Always move/copy the complete folder; do not run the EXE alone.

## Verification limitation

The supplied environment is Linux and has no .NET SDK installed. WPF targets Windows, so a real Windows executable could not be compiled or launched here. The updated GitHub Actions workflow performs the Windows publish and dependency checks on a Windows runner.
