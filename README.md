# AutoDemo-Releases

Official distribution channel and release repository for **AutoDemo**.

## Purpose
This repository serves as the public host for AutoDemo installers, releases, and update metadata (`latest.json`).
* **Source Code Repository**: AutoDemo source code remains private.
* **Auto-Update Endpoint**: Installed copies of AutoDemo query the raw content URL of `latest.json` in this repository to check for updates.

## Current Release Metadata
The updater queries `latest.json` to get:
* `version`: The latest available version.
* `downloadUrl`: Direct URL to download the release installer.
* `releaseNotes`: Brief summary of changes.

For release instructions and procedures, see [RELEASE_PROCESS.md](./RELEASE_PROCESS.md).
