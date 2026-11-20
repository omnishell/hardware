# Omni Device Manager (`device`)

# About

**`device`** is a device manager for the [Omni Shell Framework](../omni/README.md). It provides a unified CLI for managing devices of many classes — displays, networks, bluetooth, usb, pci, audio, power, backlight, input devices, and video cameras — with dynamic tab completion.

Where a module has a standard Linux tool, it uses that tool (`xrandr`, `nmcli`, `bluetoothctl`, `lsusb`, `lspci`, `pactl`/`amixer`, `xinput`, `v4l2-ctl`); where it does not, it falls back to the kernel's own interfaces through sysfs (`/sys/class/drm`, `/sys/class/net`, `/sys/bus/usb/devices`, `/sys/bus/pci/devices`, `/sys/class/input`, `/sys/class/video4linux`) or to other common tools (`amixer` instead of `pactl`, `xbacklight` instead of sysfs writes). This makes the tool work on machines that manage devices through other drivers or CLIs.

## Key Features

- **Monitor Management (`device monitor`)**: List connected displays (all, or filtered by enabled/disabled state), enable or disable outputs, set a preferred resolution, and position monitors relative to a primary or reference display (`--left` / `--right` / `--above` / `--below` / `--same-as`).
- **Network Management (`device network`)**: List all network devices with their state (nmcli, or sysfs).
- **Wireless Management (`device wifi`)**: Scan nearby networks, list registered Wi-Fi profiles, create open or WPA-PSK profiles, and connect/disconnect/remove profiles.
- **Bluetooth Management (`device bluetooth`)**: List Bluetooth devices (`--paired` for paired only) and connect, disconnect, or remove them.
- **USB Management (`device usb`)**: List USB devices via `lsusb` (or sysfs).
- **PCI Management (`device pci`)**: List PCI devices via `lspci` (or sysfs).
- **Audio Management (`device audio`)**: List audio sinks and sources, toggle default sink mute, set default sink volume (pactl, or amixer).
- **Power Management (`device power`)**: List power supplies with status and capacity from sysfs.
- **Backlight Management (`device light`)**: List backlight devices with current and max brightness; set brightness as a percent of max (sysfs, or `xbacklight`).
- **Input Device Management (`device input`)**: List input devices (xinput, or sysfs); enable or disable them.
- **Camera Management (`device camera`)**: List video capture devices via `v4l2-ctl`, `/dev/video*`, or sysfs.
- **Device Overview (`device ls`)**: List all devices across modules.

## Prerequisites

Each module has a preferred tool and portable fallbacks; the tool works without any of them by using sysfs directly:

- `xrandr` — `device monitor` (preferred); without it, falls back to sysfs `/sys/class/drm` (ls and enable/disable on writable systems)
- `nmcli` — `device network` (preferred); without it, falls back to sysfs `/sys/class/net`
- `bluetoothctl` — `device bluetooth` (required; BlueZ is the standard tool)
- `lsusb` — `device usb` (preferred); without it, falls back to sysfs `/sys/bus/usb/devices`
- `lspci` — `device pci` (preferred); without it, falls back to sysfs `/sys/bus/pci/devices`
- `pactl` — `device audio` (preferred); without it, falls back to `amixer` (ALSA)
- `amixer` — `device audio` (ALSA fallback)
- `xinput` — `device input` (preferred); without it, `device input ls` falls back to sysfs `/sys/class/input`
- `v4l2-ctl` — `device camera` (preferred); without it, falls back to `/dev/video*` and then sysfs `/sys/class/video4linux`
- `xbacklight` — `device light` (alternative to sysfs writes)

---

# Install

## Via the Official `omni` Provider

```bash
omni providers install omni
omni tools use device --provider omni
```

## From a Local Directory

```bash
omni tools use -p /path/to/omni/device
```

## Directly from GitHub

```bash
omni tools use -u https://github.com/omnishell/device.git
```

---

# Usage

```bash
device <module> <command> [options]
```

`device` with no arguments prints the full help.

## Device Overview (`device ls`)

```bash
# List all devices across modules
device ls
```

## Monitor Management (`device monitor`)

```bash
# List all connected monitors
device monitor ls

# List all monitors, including disconnected ones
device monitor ls -a|--all

# List only active/enabled monitors
device monitor ls -e|--enabled

# List only disabled monitors (connected-off and disconnected)
device monitor ls -d|--disabled

# Enable external HDMI monitor and place it to the right of eDP-1
device monitor enable HDMI-1

# Enable a monitor using its preferred resolution
device monitor enable HDMI-1 --res 1920x1080

# Turn off a monitor when undocking
device monitor disable HDMI-1

# Position <monitor> relative to <ref_monitor>; if omitted, defaults to primary
device monitor move HDMI-1 --right eDP-1
device monitor move HDMI-1 --left
device monitor move eDP-1 --above HDMI-1
device monitor move eDP-1 --below HDMI-1
device monitor move eDP-1 --same-as HDMI-1
```

## Network Devices (`device network`)

```bash
# List all network devices (wired, wireless, vpn, ...) with their state
device network ls
```

## Wireless Management (`device wifi`)

```bash
# Scan and list nearby Wi-Fi networks
device wifi ls

# List saved/registered Wi-Fi connection profiles
device wifi ls -r|--registered

# Register a new WPA-PSK Wi-Fi connection profile
device wifi set <profile_name> --id <ssid> --pass <password>
device wifi new <profile_name> --id <ssid> --pass <password>

# Register an open (unencrypted) Wi-Fi connection profile
device wifi set <profile_name> --id <ssid>

# Connect to a registered Wi-Fi profile
device wifi connect <profile_name>

# Disconnect an active Wi-Fi profile
device wifi disconnect <profile_name>

# Delete a registered Wi-Fi profile
device wifi rm <profile_name>
```

Examples:

```bash
# Register and connect to a home network
device wifi new home-net --id "MyHomeSSID" --pass "supersecret"
device wifi connect home-net

# Disconnect and remove a guest network profile
device wifi disconnect guest-wifi
device wifi rm guest-wifi
```

## Bluetooth Management (`device bluetooth`)

```bash
# List all Bluetooth devices
device bluetooth ls

# List paired Bluetooth devices only
device bluetooth ls -p|--paired

# Connect to a Bluetooth device
device bluetooth connect <addr>

# Disconnect a Bluetooth device
device bluetooth disconnect <addr>

# Remove a Bluetooth device
device bluetooth remove <addr>
```

## USB Devices (`device usb`)

```bash
# List USB devices
device usb ls
```

## PCI Devices (`device pci`)

```bash
# List PCI devices (class, vendor/device, driver)
device pci ls
```

## Audio Management (`device audio`)

```bash
# List audio sinks and sources
device audio ls

# Toggle mute of the default sink
device audio mute

# Set the default sink to unmuted
device audio unmute

# Set the default sink volume as a percent
device audio set 75
```

## Power Management (`device power`)

```bash
# List power supplies with status and capacity
device power ls
```

Example output:

```text
ADP1                 unknown       capacity=n/a
BAT0                 Full          capacity=100%
hidpp_battery_23     Discharging   capacity=n/a
```

## Backlight Management (`device light`)

```bash
# List backlight devices with current and max brightness
device light ls

# Set backlight brightness as a percent of the maximum (0-100)
device light set 75

# Set the brightness of a specific backlight device
device light set 50 intel_backlight
```

## Input Device Management (`device input`)

```bash
# List input devices
device input ls

# Disable an input device (by id)
device input disable <id>

# Enable an input device (by id)
device input enable <id>
```

## Camera Management (`device camera`)

```bash
# List video capture devices
device camera ls
```

Example output:

```text
Brio 100 (usb-0000:00:14.0-2):
	/dev/video0
	/dev/video1
	/dev/media0
```

## Compatibility

The previous short names still work: `device mon`, `device bt`, `device cam`, and `device net` dispatch to `device monitor`, `device bluetooth`, `device camera`, and `device network`. The `device monitor move` command is also available as `device monitor mv`.
