# Dynamic Island for GNOME Shell 45–49 (Full Mac-Inspired Edition v3.0)

A production-quality, accessible, lightweight **macOS Dynamic Island & MacBook Notch** extension for Fedora Linux and GNOME Shell 45, 46, 47, 48, and 49.

## Project Structure

```text
dynamic-island@gnome-extensions.local/
├── metadata.json
├── extension.js
├── stylesheet.css
├── prefs.js
├── install.sh
├── uninstall.sh
├── README.md
├── AUDIT.md
├── CHANGELOG.md
├── TESTING.md
├── schemas/
│   ├── org.gnome.shell.extensions.dynamic-island.gschema.xml
│   └── gschemas.compiled
├── modules/
│   ├── island-ui.js
│   ├── event-manager.js
│   ├── media-manager.js
│   ├── notification-manager.js
│   ├── system-monitor.js
│   ├── timer-manager.js
│   └── quick-actions.js
└── scripts/
    ├── install.sh
    ├── upgrade.sh
    ├── rollback.sh
    └── uninstall.sh
```

## Key Features (v3.0 Full Mac-Inspired Edition)

1. **MacBook Notch & Floating Pill Modes (`modules/island-ui.js`, `stylesheet.css`)**:
   - **MacBook Notch Mode**: Docks flush against the top center of the primary or selected monitor (`top-offset: 0`, square top corners, rounded `20px` bottom corners) with a simulated camera lens, status LED, and non-color-only text badges (`LOW`, `MIC`, `VPN`, `↓DL`).
   - **Hover & Click Expansion**: Smoothly expands (`Clutter.AnimationMode.EASE_OUT_EXPO`) on pointer hover or click, automatically locks open when you click any internal control so it never collapses mid-interaction, and de-emphasizes when GNOME Overview is open.
   - **3 Interactive Mac Tabs (`Nook` | `Shelf` | `Focus`)**:
     - **Nook**: MPRIS Now Playing card with album art, multi-player switcher (`⇄`), `-10s`/`+10s` seek buttons, live Battery/Audio/Brightness/CPU/RAM/Thermal/VPN telemetry pills, and Quick Actions (Focus/DND, Screenshot, Audio Settings, App Launcher).
     - **Shelf**: Quick access to pinned files (`pinned-files`) and recent files in `~/Downloads`, plus an opt-in privacy-conscious Clipboard preview.
     - **Focus**: Zero-drift monotonic Pomodoro session manager, countdown timer, and stopwatch.

2. **Real Hardware & Desktop Integrations**:
   - **Volume & Microphone HUD**: Event-driven via `Volume.getMixerControl()` (PipeWire / PulseAudio).
   - **Screen Brightness HUD**: Event-driven via `org.gnome.SettingsDaemon.Power.Screen` D-Bus proxy.
   - **Battery & Low-Power Alerts**: Event-driven via `org.freedesktop.UPower.Device` (`DisplayDevice`).
   - **Network, VPN & Bluetooth**: Event-driven via `Gio.NetworkMonitor`, `/sys/class/net` tunnel detection, and BlueZ `org.bluez.Adapter1`.
   - **Download Progress**: Event-driven via `Gio.FileMonitor` on `~/Downloads` watching active `.part`, `.crdownload`, and `.partial` transfers.

3. **Full Keyboard & Orca Screen-Reader Accessibility**:
   - Every control defines `can_focus: true`, `Atk.Role`, `accessible_name`, and visible `:focus` rings.
   - Keyboard shortcuts: `<Super>i` (toggle island), `<Super><Shift>i` (cycle Nook/Shelf/Focus tabs), `<Super><Shift>t` (start/pause timer), `<Super><Shift>p` (play/pause media), `Page_Up`/`Page_Down` (switch tabs when open), and `Escape` (dismiss and restore previous window focus).

## Optional Dependencies & Features Matrix

All core features work out of the box on a standard Fedora Workstation installation. Optional system services enable specific hardware indicators when present:

| Feature | Underlying Interface / Service | Behavior When Unavailable |
| :--- | :--- | :--- |
| **MPRIS Media Controls & Seeking** | Any player exposing `org.mpris.MediaPlayer2.Player` (Spotify, VLC, Rhythmbox, Amberol, Firefox, Chromium) | Displays graceful `"Not Playing"` state; seek buttons auto-hide if player sets `CanSeek = false`. |
| **Screen Brightness HUD** | `org.gnome.SettingsDaemon.Power.Screen` (laptop backlight or supported DDC display) | Automatically hidden on desktop monitors without backlight control (`brightnessSupported = false`). |
| **Battery & Charger Alerts** | `org.freedesktop.UPower` (`DisplayDevice`) | Displays `"AC Power"` on desktop workstations without a battery (`batteryPresent = false`). |
| **Bluetooth Status** | `org.bluez` (`/org/bluez/hci0`) | Defaults cleanly to `"BT Off"` if no Bluetooth adapter is installed. |
| **CPU Thermal Readout** | `/sys/class/thermal/thermal_zone0/temp` | Shows CPU % and RAM % only if the kernel/VM does not expose a thermal zone (`tempSupported = false`). |
| **Schema Recompilation** | `glib-compile-schemas` (`glib2` / `glib2-devel` package) | Uses pre-compiled `schemas/gschemas.compiled` bundled inside the release ZIP. |

## Installation, Upgrade, Rollback & Uninstallation

### 1. Fresh Install or Upgrade (Preserves User Settings)
```bash
unzip -o gnome-dynamic-island.zip
cd dynamic-island@gnome-extensions.local
./scripts/upgrade.sh
```
*(Or extract directly into `~/.local/share/gnome-shell/extensions/`: `unzip -o gnome-dynamic-island.zip -d ~/.local/share/gnome-shell/extensions/`)*

### 2. Activating on Fedora Wayland vs. X11
- **Wayland (Fedora Default)**: Log out of your desktop session and log back in once so GNOME Shell discovers the extension directory, then run:
  ```bash
  gnome-extensions enable dynamic-island@gnome-extensions.local
  gnome-extensions prefs dynamic-island@gnome-extensions.local
  ```
- **X11**: Press `Alt+F2`, type `r`, and press `Enter`, then run `gnome-extensions enable dynamic-island@gnome-extensions.local`.

### 3. Rollback to Previous Backup
Every run of `install.sh` or `upgrade.sh` saves a timestamped backup and dconf snapshot in `~/.local/share/gnome-dynamic-island-backups/latest`. To roll back:
```bash
./scripts/rollback.sh
```

### 4. Uninstallation
```bash
# Remove only this extension's files (keep saved preferences in dconf)
./scripts/uninstall.sh

# Remove extension files AND erase saved preferences in dconf
./scripts/uninstall.sh --purge-settings
```

## Remaining Limitations
- **Wayland Session Reload**: GNOME Shell on Wayland acts as the display server and does not support reloading JS extensions in-place without logging out and back in (or running a nested `dbus-run-session -- gnome-shell --nested --wayland` instance).
- **Per-Byte Download Percentage**: Because Linux browsers (Firefox, Chrome) write to `.part` or `.crdownload` files without broadcasting byte-level progress over a unified desktop D-Bus specification, the download monitor tracks active transfer count (`↓N`) and completion events via `Gio.FileMonitor`.
# -Dynamic-Island-for-GNOME
