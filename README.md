# Ravix Browser

A modern Android browser built on **GeckoView** (Mozilla's Firefox engine), with full WebExtension support, tab groups, user scripts, background persistence, and a clean Material 3 UI.

---

## Features

| Feature | Details |
|---|---|
| **Engine** | GeckoView 150 (Firefox 150) |
| **Extensions** | Install/enable/disable WebExtensions from AMO; sideload any `.xpi` |
| **Multi-tab** | Unlimited tabs with tab groups, color labels, pinning, and quick-switch drawer |
| **Tab groups** | Named groups with 9-color palette; drag-to-reorder in tab manager |
| **User scripts** | Tampermonkey-compatible userscript manager with `@match`, `@require`, `run-at`; auto-update |
| **Background service** | Persistent foreground service (mediaPlayback + dataSync types) |
| **Persistent notification** | Shows current page title, tab count, quick actions |
| **Picture-in-Picture** | Native Android PiP for video |
| **Reader mode** | Distraction-free reading view |
| **Page translation** | Translate any page to a chosen language |
| **Pull-to-refresh** | Custom swipe-to-reload gesture |
| **PWA install** | Install websites as home-screen apps |
| **Web Push** | Full Web Push Notifications support |
| **Password manager** | Save/autofill credentials; biometric unlock |
| **Cookie manager** | Per-site cookie inspection and control |
| **Safe Browsing** | Google Safe Browsing v4 (bring your own API key) |
| **Fingerprint protection** | Spoof canvas, WebGL, audio, and hardware APIs |
| **Downloads** | Download manager with progress tracking and speed display |
| **Bookmarks** | Folders, import/export HTML |
| **History** | Searchable, indexed for fast access |
| **HTTPS-only mode** | Force secure connections globally |
| **Enhanced Tracking Protection** | Built-in tracker + ad blocking |
| **Fullscreen** | Full browser fullscreen + scroll-driven auto-hide chrome |
| **Print** | Print any page via Android print service |
| **Context menu** | Long-press links/images for copy, open in new tab, save, share |
| **Set as default browser** | Register Ravix as the system default |
| **Self-update** | Checks GitHub Releases and installs APK updates in-app |
| **License system** | Key-based activation with offline grace period (7 days) |
| **Navigation bar** | Back, Forward, Home, Tabs, Menu |
| **Private tabs** | Isolated sessions with tracking protection |
| **Appearance** | Light / Dark / System theme; compact toolbar option; font size scale |

---

## Setup & Build

### Requirements
- Android Studio Hedgehog (2023.1.1) or later
- JDK 17
- Android SDK API 35
- Min SDK: API 26 (Android 8.0)

### Build Steps

```bash
# Clone / unzip the project
cd Ravix

# Build debug APK
./gradlew assembleDebug

# Build release APK
./gradlew assembleRelease

# Install on connected device
./gradlew installDebug
```

The APK will be at:
```
app/build/outputs/apk/debug/app-debug.apk
app/build/outputs/apk/release/app-release.apk
```

---

## Project Structure

```
Ravix/
├── app/src/main/
│   ├── AndroidManifest.xml
│   ├── assets/
│   │   └── newtab.html                     # Custom new-tab page
│   └── java/com/ravix/browser/
│       ├── ui/
│       │   ├── BrowserActivity.kt           # Main browser UI (~5900 lines)
│       │   ├── BrowserSwipeRefreshLayout.kt # Custom pull-to-refresh
│       │   ├── tabs/
│       │   │   ├── TabManagerActivity.kt    # Full tab grid/list switcher
│       │   │   ├── SectionedTabAdapter.kt   # Grouped + ungrouped tab adapter
│       │   │   ├── GroupMemberAdapter.kt    # Tab group member list
│       │   │   └── TabActions.kt           # Tab context menu actions
│       │   ├── extensions/
│       │   │   ├── ExtensionManagerActivity.kt  # Install/manage extensions
│       │   │   └── ExtensionPopupActivity.kt    # Extension popup windows
│       │   ├── scripts/
│       │   │   ├── ScriptManagerActivity.kt # Userscript list
│       │   │   └── ScriptEditorActivity.kt  # Userscript editor
│       │   ├── bookmarks/
│       │   │   └── BookmarksActivity.kt
│       │   ├── history/
│       │   │   └── HistoryActivity.kt
│       │   ├── downloads/
│       │   │   └── DownloadsActivity.kt
│       │   └── settings/
│       │       └── SettingsActivity.kt
│       ├── license/
│       │   ├── LicenseCheckActivity.kt      # Launch gate (validates key on start)
│       │   ├── LicenseActivity.kt           # Key entry / activation screen
│       │   └── LicenseManager.kt           # Activation, caching, grace period
│       ├── service/
│       │   ├── BrowserBackgroundService.kt  # Foreground service + notification
│       │   └── BootReceiver.kt             # Auto-start on boot
│       ├── scripts/
│       │   ├── ScriptInjector.kt           # Tampermonkey-compatible injection engine
│       │   └── FingerprintProtection.kt    # Canvas/WebGL/audio spoofing
│       ├── data/
│       │   ├── db/BrowserDatabase.kt       # Room database (v3)
│       │   ├── db/Daos.kt
│       │   ├── model/Entities.kt           # Tab, Bookmark, History, Download, Script, Password, Group
│       │   ├── model/TabGroupPalette.kt    # 9-color group label palette
│       │   └── repository/BrowserRepository.kt
│       └── utils/
│           ├── BrowserViewModel.kt         # Tab state management
│           ├── AppUpdater.kt               # GitHub Releases self-updater
│           └── InsetsUtil.kt
│   └── res/
│       ├── layout/                         # All XML layouts
│       ├── drawable/                       # Vector icons + backgrounds (day/night)
│       ├── mipmap-*/                       # App icon (all densities)
│       ├── values/                         # Strings, colors, themes, arrays
│       └── xml/                            # Preferences, file_paths, backup rules
```

---

## Extension Support

Ravix supports installing WebExtensions directly from [addons.mozilla.org](https://addons.mozilla.org), or by sideloading any `.xpi` file:

1. Open menu → **Extensions**
2. Tap **Install** next to any listed extension, or navigate to an `.xpi` URL to sideload
3. The XPI is fetched and installed via GeckoView's `WebExtensionController`

Pre-listed extensions:
- uBlock Origin `1.70.0`
- Privacy Badger `2026.2.20`
- Dark Reader `4.9.125`

Unlisted/sideloaded extensions (e.g. Tampermonkey) appear in the manager automatically after install.

---

## User Scripts

Ravix includes a built-in Tampermonkey-compatible userscript engine:

- Install scripts from `.user.js` URLs (GreasyFork, OpenUserJS, etc.) or paste code directly
- Supports `@match`, `@include`, `@exclude`, `@run-at`, `@require` headers
- `run-at` modes: `document_start`, `document_end` (default), `document_idle`
- `@require` libraries are fetched once, cached in memory, and prepended to the script
- Scripts auto-update from `@updateURL` / `@downloadURL` or the original install URL
- Full in-app editor with enable/disable toggle per script

---

## Tab Groups

- Create named groups with a color label (9 colors: Purple, Blue, Teal, Green, Amber, Orange, Red, Pink, Gray)
- Assign/move tabs between groups from the tab manager or the quick-switch drawer
- Groups persist across sessions via Room database
- Drag-to-reorder tabs within and across groups

---

## Background Running

- `BrowserBackgroundService` starts as a **foreground service** on app launch (`mediaPlayback | dataSync` types)
- A **persistent notification** shows the current page title, URL, and tab count
- Notification actions: **New Tab**, **Stop**
- A `PowerManager.WakeLock` keeps the CPU active with the screen off
- `BootReceiver` restarts the service on device reboot
- Background running and the persistent notification can each be toggled independently in Settings

---

## License System

Ravix uses key-based activation:

- On first launch, `LicenseCheckActivity` validates the stored key against the remote server
- A **1-hour quick-check TTL** skips the network call on repeat launches, making startup instant
- Keys are re-validated with the server every **24 hours**
- A **7-day grace period** allows offline use during server outages
- Revoked keys are rejected on the next successful server contact

---

## Self-Update

Ravix checks [GitHub Releases](https://github.com/zen-chronoa/ravix-browser/releases) for new APK builds:

1. Open menu → **Check for Updates**
2. If a newer `versionCode` is found, the APK asset is downloaded to the app cache
3. The system installer is launched automatically

---

## Settings Reference

| Category | Setting |
|---|---|
| **General** | Default search engine, JavaScript, Cookies, Custom homepage, Font size |
| **Privacy & Security** | Enhanced Tracking Protection, HTTPS-Only mode, Do Not Track, Safe Browsing (+ API key), Password Autofill, Fingerprint Protection, Clear Browsing Data |
| **Background & Performance** | Run in Background, Persistent Notification |
| **Appearance** | Theme (Light / Dark / System), Compact Toolbar |
| **Data & Privacy** | Selective clear (cookies / cache / history / permissions / passwords), Export bookmarks (HTML), Import bookmarks (HTML) |
| **Default Browser** | Set Ravix as system default |

---

## Permissions Used

| Permission | Purpose |
|---|---|
| `INTERNET` | Web browsing |
| `ACCESS_NETWORK_STATE` | Network status checks |
| `FOREGROUND_SERVICE` | Background service |
| `FOREGROUND_SERVICE_DATA_SYNC` | Background data sync service type |
| `FOREGROUND_SERVICE_MEDIA_PLAYBACK` | Background media playback service type |
| `POST_NOTIFICATIONS` | Persistent notification |
| `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` | Prevent system from killing the background service |
| `WAKE_LOCK` | Screen-off background operation |
| `RECEIVE_BOOT_COMPLETED` | Auto-start on boot |
| `WRITE_EXTERNAL_STORAGE` | Downloads on Android ≤ 9 (API 28 max) |
| `CAMERA` | WebRTC / media on pages |
| `RECORD_AUDIO` | WebRTC / media on pages |
| `MODIFY_AUDIO_SETTINGS` | Audio focus management |
| `VIBRATE` | Haptic feedback |
| `REQUEST_INSTALL_PACKAGES` | In-app APK self-update |
| `USE_BIOMETRIC` | Biometric unlock for password manager |
| `ACCESS_FINE_LOCATION` | Geolocation API |
| `ACCESS_COARSE_LOCATION` | Geolocation API (coarse fallback) |

---

## License

MIT License — build freely.
