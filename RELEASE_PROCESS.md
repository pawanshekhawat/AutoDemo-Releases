# AutoDemo Release Process Guide (Tauri v2 Updater)

This document describes how to build, test, and publish a new release of AutoDemo with signed in-app update capabilities.

---

## Step 1: Version Update & Synchronization

All three version indicators must match:
1. **`package.json`**: `"version": "X.Y.Z-beta.N"`
2. **`src-tauri/tauri.conf.json`**: `"version": "X.Y.Z-beta.N"`
3. **Release Git Tag**: `vX.Y.Z-beta.N`

If they differ, the build process will fail immediately.

### Action
Update the version strings in both `package.json` and `src-tauri/tauri.conf.json`.
For example, to update to `0.1.0-beta.3`:
* In `package.json`:
  ```json
  "version": "0.1.0-beta.3"
  ```
* In `src-tauri/tauri.conf.json`:
  ```json
  "version": "0.1.0-beta.3"
  ```

---

## Step 2: Build the Signed Application

Build the signed production installer and signature metadata. 

### Local PowerShell Instructions
1. Expose the release tag environment variable.
2. Load and trim the private updater signing key.
3. Expose the empty key password (since the key was generated without a password).
4. Run the Tauri build.

```powershell
# Set release tag
$env:TAURI_RELEASE_TAG="v0.1.0-beta.3"

# Load private key from its secure local path
$env:TAURI_SIGNING_PRIVATE_KEY = (Get-Content -Raw -Path src-tauri/autodemo-updater.key).Trim()

# Define empty password to skip interactive prompt in non-interactive builds
$env:TAURI_SIGNING_PRIVATE_KEY_PASSWORD=""

# Compile and package
npm run tauri build
```

This generates the following assets in `src-tauri/target/release/bundle/nsis/`:
* **Installer**: `AutoDemo_0.1.0-beta.3_x64-setup.exe`
* **Signature**: `AutoDemo_0.1.0-beta.3_x64-setup.exe.sig`

---

## Step 3: Create GitHub Release

1. Navigate to the **AutoDemo-Releases** repository:
   [https://github.com/pawanshekhawat/AutoDemo-Releases](https://github.com/pawanshekhawat/AutoDemo-Releases)
2. Go to **Releases** -> **Draft a new release**.
3. Create a new tag (e.g. `v0.1.0-beta.3`) matching the version exactly.
4. Set the Release Title to match (e.g. `v0.1.0-beta.3`).
5. Attach the compiled installer asset:
   `AutoDemo_0.1.0-beta.3_x64-setup.exe`
6. Publish the release as a pre-release or stable release.

---

## Step 4: Update and Commit `updater-v2.json`

1. Open the generated signature file `src-tauri/target/release/bundle/nsis/AutoDemo_0.1.0-beta.3_x64-setup.exe.sig` and copy its entire single-line text content.
2. Open `updater-v2.json` in the releases repository.
3. Update the values to match the new release:
   ```json
   {
     "version": "0.1.0-beta.3",
     "notes": "✓ Detailed list of bugfixes and features for this version.",
     "pub_date": "2026-06-10T16:25:00+05:30",
     "platforms": {
       "windows-x86_64": {
         "signature": "PASTE_THE_ENTIRE_CONTENT_OF_THE_SIG_FILE_HERE",
         "url": "https://github.com/pawanshekhawat/AutoDemo-Releases/releases/download/v0.1.0-beta.3/AutoDemo_0.1.0-beta.3_x64-setup.exe"
       }
     }
   }
   ```
4. Commit and push the updated `updater-v2.json` file to the `main` branch of the `AutoDemo-Releases` repository.

Once pushed, all active installations running older versions will detect the update and perform a seamless in-app upgrade next time they check for updates.

---

## Future Release Checklist

Use this checklist for every future version release to ensure a smooth, error-free deployment:

- [ ] **Sync Local Versions**
  - Update `"version"` in `package.json` to `"X.Y.Z-beta.N"`.
  - Update `"version"` in `src-tauri/tauri.conf.json` to `"X.Y.Z-beta.N"`.
- [ ] **Pre-build Validation & Signed Compilation**
  - Run TypeScript compile check: `npx tsc --noEmit`.
  - Set release environment variables:
    * `$env:TAURI_RELEASE_TAG="vX.Y.Z-beta.N"`
    * `$env:TAURI_SIGNING_PRIVATE_KEY="(key_file_content)"`
    * `$env:TAURI_SIGNING_PRIVATE_KEY_PASSWORD=""`
  - Compile the production bundle: `npm run tauri build`.
  - Confirm both the installer (`.exe`) and signature file (`.exe.sig`) were successfully created.
- [ ] **Create GitHub Release**
  - Draft a new release on `AutoDemo-Releases` with tag `vX.Y.Z-beta.N`.
  - Upload the installer `AutoDemo_X.Y.Z_x64-setup.exe` as the asset.
  - Publish the release.
- [ ] **Update Update Metadata (`updater-v2.json`)**
  - Open `updater-v2.json` in the releases repository.
  - Update `"version"` to `"X.Y.Z-beta.N"`.
  - Paste the **entire** content of `.exe.sig` into the `"signature"` field.
  - Update `"url"` to point to the newly uploaded asset.
  - Set the current RFC 3339 timestamp in `"pub_date"`.
  - Save, commit, and push `updater-v2.json` to the `main` branch.
- [ ] **End-to-End Verification**
  - Run an older version of the application and trigger the update button.
  - Verify download progress, signature check, installation, and automatic restart succeed.
