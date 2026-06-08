# AutoDemo Release Process Guide

This document describes how to build, test, and publish a new release of AutoDemo.

---

## Step 1: Version Update & Synchronization

All three version indicators must match:
1. **`package.json`**: `"version": "X.Y.Z-beta.N"`
2. **`src-tauri/tauri.conf.json`**: `"version": "X.Y.Z-beta.N"`
3. **Release Git Tag**: `vX.Y.Z-beta.N`

If they differ, the build process will fail immediately.

### Action
Update the version strings in both `package.json` and `src-tauri/tauri.conf.json`.
For example, to update to `0.1.0-beta.2`:
- In `package.json`:
  ```json
  "version": "0.1.0-beta.2"
  ```
- In `src-tauri/tauri.conf.json`:
  ```json
  "version": "0.1.0-beta.2"
  ```

---

## Step 2: Build the Application

Build the production binaries. This automatically runs version validation.

```bash
# Set environment variable for validation check
$env:TAURI_RELEASE_TAG="v0.1.0-beta.2"

# Compile and package the Tauri app
npm run tauri build
```

This generates the installer assets in:
`src-tauri/target/release/bundle/nsis/`

The primary installer file will be:
`AutoDemo_0.1.0-beta.2_x64-setup.exe`

---

## Step 3: Create GitHub Release

1. Navigate to the **AutoDemo-Releases** repository:
   [https://github.com/pawanshekhawat/AutoDemo-Releases](https://github.com/pawanshekhawat/AutoDemo-Releases)
2. Go to **Releases** -> **Draft a new release**.
3. Create a new tag (e.g. `v0.1.0-beta.2`) matching the version exactly.
4. Set the Release Title to match (e.g. `v0.1.0-beta.2`).
5. Attach the compiled installer asset:
   `AutoDemo_0.1.0-beta.2_x64-setup.exe`
6. Publish the release.

---

## Step 4: Update and Commit `latest.json`

1. Open `latest.json` in this repository.
2. Update the values to match the new release:
   ```json
   {
     "version": "0.1.0-beta.2",
     "downloadUrl": "https://github.com/pawanshekhawat/AutoDemo-Releases/releases/download/v0.1.0-beta.2/AutoDemo_0.1.0-beta.2_x64-setup.exe",
     "releaseNotes": "Fixes recording issues and adds system diagnostics improvements."
   }
   ```
3. Commit and push the updated `latest.json` file to the `main` branch of the `AutoDemo-Releases` repository.

Once pushed, all active installations will immediately detect the update on next launch.

---

## Future Release Checklist

Use this checklist for every future version release to ensure a smooth, error-free deployment:

- [ ] **Sync Local Versions**
  - Update `"version"` in `package.json` to `"X.Y.Z-beta.N"`.
  - Update `"version"` in `src-tauri/tauri.conf.json` to `"X.Y.Z-beta.N"`.
- [ ] **Pre-build Validation & Compilation**
  - Run TypeScript compile check: `npx tsc --noEmit`.
  - Set release environment variable: `$env:TAURI_RELEASE_TAG="vX.Y.Z-beta.N"`.
  - Compile the production bundle: `npm run tauri build`.
  - Confirm the NSIS installer was successfully created: `src-tauri/target/release/bundle/nsis/AutoDemo_X.Y.Z_x64-setup.exe`.
- [ ] **Create GitHub Release**
  - Go to [AutoDemo-Releases Releases](https://github.com/pawanshekhawat/AutoDemo-Releases/releases).
  - Draft a new release with tag `vX.Y.Z-beta.N` and title `AutoDemo X.Y.Z Beta N`.
  - Upload the installer `AutoDemo_X.Y.Z_x64-setup.exe` as the primary asset.
  - Publish the release as a pre-release.
- [ ] **Update Update Metadata (`latest.json`)**
  - Open `latest.json` in the releases repository.
  - Update `"version"` to `"X.Y.Z-beta.N"`.
  - Update `"downloadUrl"` to `https://github.com/pawanshekhawat/AutoDemo-Releases/releases/download/vX.Y.Z-beta.N/AutoDemo_X.Y.Z_x64-setup.exe`.
  - Add descriptive `"releaseNotes"` as an array of strings.
  - Save, commit, and push `latest.json` to the `main` branch.
- [ ] **End-to-End Verification**
  - Verify `latest.json` is reachable at `https://raw.githubusercontent.com/pawanshekhawat/AutoDemo-Releases/main/latest.json`.
  - Start the application and check that it bypasses the update overlay (since version matches).
  - Expand diagnostic info in the settings/about screen to verify correct version metadata display.

