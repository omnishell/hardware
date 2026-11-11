# Omni Hardware Manager (`hardware`)

# About

**`hardware`** is a display and wireless network configuration tool for the [Omni Shell Framework](../omni/README.md). It provides a unified CLI with programmable tab completion over `xrandr` (for managing single and multi-monitor layouts) and `nmcli` / NetworkManager (for scanning, registering, connecting, and disconnecting Wi-Fi networks).

## Key Features

- **Monitor Management (`hardware mon`)**: List connected displays (filtered by active/enabled or disabled state), enable or disable outputs, and position monitors relative to a primary or reference display (`--left` / `--right`) with dynamic tab completion of connected display names.
- **Wi-Fi Management (`hardware wifi`)**: Scan nearby access points, list registered NetworkManager Wi-Fi profiles, create open or WPA-PSK profiles, and connect/disconnect/remove profiles with dynamic tab completion.

## Prerequisites

- `xrandr` (required for `hardware mon` commands)
- `nmcli` / NetworkManager (required for `hardware wifi` commands)

---

# Install

## Via the Official `omni` Provider

```bash
omni providers install omni
omni tools use hardware --provider omni
```

## From a Local Directory

```bash
omni tools use -p /path/to/omni/hardware
```

## Directly from GitHub

```bash
omni tools use -u https://github.com/omnishell/hardware.git
```

---

# Usage

```bash
hardware <mon|wifi> <subcommand> [options]
```

## Monitor Management (`hardware mon`)

```bash
# List all connected monitors
hardware mon ls

# List only active/enabled monitors
hardware mon ls -e|--enabled

# List only disabled monitors
hardware mon ls -d|--disabled

# Enable a monitor using its preferred resolution (`xrandr --output <mon> --auto`)
hardware mon enable <monitor>

# Turn off a monitor (`xrandr --output <mon> --off`)
hardware mon disable <monitor>

# Position <monitor> to the left or right of a reference monitor
# (If <ref_monitor> is omitted, defaults to the primary monitor)
hardware mon mv <monitor> -l|--left [<ref_monitor>]
hardware mon mv <monitor> -r|--right [<ref_monitor>]
```

### Monitor Examples

```bash
# Check connected displays
hardware mon ls

# Enable external HDMI monitor and place it to the right of eDP-1
hardware mon enable HDMI-1
hardware mon mv HDMI-1 --right eDP-1

# Disable external monitor when undocking
hardware mon disable HDMI-1
```

---

## Wi-Fi Management (`hardware wifi`)

```bash
# Scan and list nearby Wi-Fi networks
hardware wifi ls

# List saved/registered Wi-Fi connection profiles
hardware wifi ls -r|--registered

# Register a new WPA-PSK Wi-Fi connection profile
hardware wifi set <profile_name> --id <ssid> --pass <password>
hardware wifi new <profile_name> --id <ssid> --pass <password>

# Register an open (unencrypted) Wi-Fi connection profile
hardware wifi set <profile_name> --id <ssid>

# Connect to a registered Wi-Fi profile
hardware wifi connect <profile_name>

# Disconnect an active Wi-Fi profile
hardware wifi disconnect <profile_name>

# Delete a registered Wi-Fi profile
hardware wifi rm <profile_name>
```

### Wi-Fi Examples

```bash
# Register and connect to a home network
hardware wifi new home-net --id "MyHomeSSID" --pass "supersecret"
hardware wifi connect home-net

# Disconnect and remove a guest network profile
hardware wifi disconnect guest-wifi
hardware wifi rm guest-wifi
```
