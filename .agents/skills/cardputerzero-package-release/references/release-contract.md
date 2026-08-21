# Release And Upload Contract

## Build Outputs

Use repository presets rather than custom build directories:

- desktop configure presets: `darwin-arm64`, `darwin-x86-64`, `linux-x86-64`, `win32-msvc`, and
  `win32-mingw64`;
- device presets: `cp0-native` and `cp0-cross`;
- release workflow: `cp0-cross-package`;
- Debian package output: `dist/<APP_DISPLAY_NAME>_<version>_<revision>_arm64.deb`.

`cmake/cm0-package.cmake` is the installation/package contract. `app-builder.json` is the final
upload/store contract. Neither replaces the other.

## Current app-builder.json Fields

| Field | Release rule |
| --- | --- |
| `app_name` | User-facing app name. |
| `package_name` | Stable upload/application identifier; currently expected to match the CMake project name. |
| `version` | Must match `project(... VERSION ...)`. |
| `icon` | Repository-relative file that must exist. |
| `screenshots` | Non-empty repository-relative list; every file must exist. |
| `description` | Localized upload descriptions. |
| `categories` | Non-empty upload categories. |
| `permissions` | Boolean declarations for shipped behavior, not driver enablement. |
| `author`, `author_mail` | Release ownership metadata. |
| `source_repo` | Actual source repository for this application. |
| `license` | Project distribution license. |
| `share_code` | Upload-system value; do not invent or rotate it. |

The current permission keys are `camera`, `microphone`, `imu`, `network`,
`additional_hardware`, `background_service`, and `external_display`.

## Permission Mapping

- Camera capture: `camera`.
- Microphone/recording: `microphone`.
- IMU reads: `imu`.
- Network access or scans: `network`.
- GPIO, I2C, SPI, UART, USB accessory, IR, or other external buses: `additional_hardware` when the
  upload policy defines them that way.
- Installed background service: `background_service`.
- HDMI or other external display output: `external_display`.

Confirm ambiguous mappings against the uploader's current schema. A `true` value does not install
a library, grant Linux device permissions, add polkit/udev rules, or prove physical support.

## Pre-upload Checklist

1. Working tree and requested release scope are understood.
2. Host build succeeds for changes that can run on desktop.
3. `cp0-cross-package` succeeds.
4. Artifact is an ARM64 Debian package.
5. Package control metadata and installed file list are correct.
6. `app-builder.json` version, identity, permissions, and asset paths match the shipped artifact.
7. Store icon and screenshots describe the current UI.
8. Upload destination, tool, credentials, and exact artifact are explicit.

`deploy.sh` copies a package to a device and is not the package-store upload flow.

