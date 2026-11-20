# Omni Device Manager (`device`)

# About

**`device`** is a device manager for the [Omni Shell Framework](../omni/README.md). It provides a unified CLI for managing devices of many classes — monitors, networks, Bluetooth, USB, PCI, audio, power, backlight, input devices, and video cameras — with dynamic tab completion of device names where available.

## Key Features

- **Monitor Management (`device mon`)**: List connected displays (all, or filtered by enabled/disabled state), enable or disable outputs, set a preferred resolution, and position monitors relative to a primary or reference display (`--left` / `--right` / `--above` / `--below` / `--same-as`).
- **Network Management (`device wifi`, `device net`)**: `wifi` scans nearby networks, lists registered Wi-Fi profiles, creates open or WPA-PSK profiles, and connects/disconnects/removes profiles; `net` lists all network devices.
- **Bluetooth Management (`device bt`)**: List Bluetooth devices (`--paired` for paired only) and connect, disconnect, or remove them.
- **USB Management (`device usb`)**: List USB devices via `lsusb`.
- **PCI Management (`device pci`)**: List PCI devices via `lspci -nnk`.
- **Audio Management (`device audio`)**: List audio sinks and sources, toggle default sink mute, set default sink volume.
- **Power Management (`device power`)**: List power supplies with status and capacity from sysfs.
- **Backlight Management (`device light`)**: List backlight devices with current and max brightness; set brightness as a percent of max.
- **Input Device Management (`device input`)**: List input devices; enable or disable them.
- **Camera Management (`device cam`)**: List video capture devices via `v4l2-ctl`.
- **Device Overview (`device ls`)**: List all devices across modules.

## Prerequisites

Each module uses a standard Linux tool; install the ones you need:

- `xrandr` — required for `device mon` (display management)
- `nmcli` — required for `device wifi` and `device net`
- `bluetoothctl` — required for `device bt`
- `lsusb` — required for `device usb`
- `lspci` — required for `device pci`
- `pactl` — required for `device audio`
- `xinput` — required for `device input`
- `v4l2-ctl` — required for `device cam` (falls back to `/dev/video*`)
- `/sys/class/power_supply` — `device power` (available on Linux by default)
- `/sys/class/backlight` — `device light` (available on Linux by default; root needed to change brightness)

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

## Monitor Management (`device mon`)

```bash
# List all connected monitors
device mon ls

# List all monitors, including disconnected ones
device mon ls -a|--all

# List only active/enabled monitors
device mon ls -e|--enabled

# List only disabled monitors (connected-off and disconnected)
device mon ls -d|--disabled

# Enable external HDMI monitor and place it to the right of eDP-1
device mon enable HDMI-1

# Enable a monitor using its preferred resolution
device mon enable HDMI-1 --res 1920x1080

# Turn off a monitor when undocking
device mon disable HDMI-1

# Position <monitor> relative to <ref_monitor>; if omitted, defaults to primary
device mon mv HDMI-1 --right eDP-1
device mon mv HDMI-1 --left
device mon mv eDP-1 --above HDMI-1
device mon mv eDP-1 --below HDMI-1
device mon mv eDP-1 --same-as HDMI-1
```

## Wi-Fi Management (`device wifi`)

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

## Network Devices (`device net`)

```bash
# List all network devices (wired, wireless, vpn, ...) with their state
device net ls
```

## Bluetooth Management (`device bt`)

```bash
# List all Bluetooth devices
device bt ls

# List paired Bluetooth devices only
device bt ls -p|--paired

# Connect to a Bluetooth device
device bt connect <addr>

# Disconnect a Bluetooth device
device bt disconnect <addr>

# Remove a Bluetooth device
device bt rm <addr>
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

## Camera Management (`device cam`)

```bash
# List video capture devices
device cam ls
```

Example output:

```text
Brio 100 (usb-0000:00:14.0-2):
	/dev/video0
	/dev/video1
	/dev/media0
```
