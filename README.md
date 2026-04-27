# LG Display Controller — Q-SYS Plugin

A Q-SYS scripted component plugin for controlling LG commercial displays via **RS-232C** or **TCP/IP** (port 9761), with optional **Wake-on-LAN** power-on support.

## Features

- **Dual Transport** — RS-232 (9600/8/N/1) or Ethernet TCP (port 9761)
- **Display Type Profiles** — Switch between *Commercial Signage* and *Pro:Centric* input source maps via a dropdown
- **Wake-on-LAN** — Optional WOL magic-packet power-on with configurable MAC address
- **Display Controls** — Power, audio mute, screen mute (blank), input source selection, volume, brightness, contrast, aspect ratio
- **Channel Controls** — Channel up/down, direct tune (major/minor), and 6 favorite channel presets with set/recall
- **Automatic Polling** — Configurable polling interval keeps all status indicators in sync
- **Startup Source** — Automatically switch to a configured input after power-on confirmation
- **Multi-Display** — Configurable Set ID (0–99) for RS-232 daisy-chain environments

## Installation

1. Download `QSYS-LG_Display_Controller.qplug`
2. Copy it into your Q-SYS Designer plugin directory:
   - Default: `%USERPROFILE%\Documents\QSC\Q-SYS Designer\Plugins`
3. Restart Q-SYS Designer
4. The plugin appears under **Scripted Components** as **"LG Display"**

## Configuration

### Plugin Properties

| Property | Type | Default | Description |
|---|---|---|---|
| **Connection Type** | Dropdown | `TCP` | `TCP` or `RS-232` |
| **IP Address** | Text | `192.168.1.100` | Display IP (TCP mode only) |
| **Set ID** | Integer | `1` | LG Set ID (0 = broadcast, 1–99 = individual) |
| **Poll Interval** | Integer | `5` | Status polling interval in seconds (1–30) |
| **Display Type** | Dropdown | `Commercial Signage` | Switches available input sources between Signage and Pro:Centric profiles |
| **Power On Method** | Dropdown | `Standard` | `Standard` (RS-232/TCP command) or `Wake on LAN` |
| **MAC Address** | Text | *(empty)* | Display MAC address for WOL (format: `AA:BB:CC:DD:EE:FF`) |
| **Startup Source** | Dropdown | `None` | Auto-switch to this input after power-on |

### RS-232 Setup

1. Set **Connection Type** to `RS-232`
2. Wire the serial pin to your Q-SYS Core serial port
3. Cable: Null-modem / crossover RS-232 to the display's RS-232C port
4. Settings: **9600 baud, 8 data bits, No parity, 1 stop bit**

### TCP/IP Setup

1. Set **Connection Type** to `TCP`
2. Enter the display's **IP Address**
3. Ensure **IP Control** is enabled on the display:
   - Navigate to the installer/setup menu
   - Enable "Network IP Control"
4. The plugin connects on **port 9761**

### Wake-on-LAN Setup

1. Set **Power On Method** to `Wake on LAN`
2. Enter the display's **MAC Address** (e.g., `AB:CD:EF:12:34:56`)
3. Ensure the display and Q-SYS Core are on the same network/VLAN
4. When Power On is pressed, the plugin sends a WOL magic packet first, then follows up with the standard power-on command after 3 seconds

## Input Source Profiles

### Commercial Signage
HDMI 1–4, DisplayPort, DVI-D, USB, OPS/PC

### Pro:Centric
HDMI 1–4, Component, AV, USB, TV Tuner

## Controls Reference

### Display Control Section
| Control | Function |
|---|---|
| **ON / OFF** | Power on/off (green/red buttons) |
| **MUTE** | Toggle audio mute |
| **BLANK** | Toggle screen mute (video blanking) |
| **Source Buttons** | Select input (radio-group behavior) |
| **VOL +/−** | Adjust volume |
| **Bright +/−** | Adjust brightness |
| **Contrast +/−** | Adjust contrast |
| **Status Indicators** | Power state, mute state, current source, volume level, brightness, contrast, aspect ratio |
| **Connection LED** | Green = connected, Off = disconnected |

### Channel Control Section
| Control | Function |
|---|---|
| **CH +/−** | Channel up/down |
| **Major / Minor** | Direct channel number entry |
| **TUNE** | Execute direct tune with entered major/minor |
| **FAV 1–6** | Recall a saved favorite channel |
| **SET** | Store current major/minor to a favorite slot |

> **Note:** Channel controls require a display with a built-in RF tuner (e.g., Pro:Centric hotel TVs). Signage panels without tuners will not respond to channel commands.

## LG Protocol Reference

Command format: `[cmd1][cmd2] [setID] [data]\r`

| Function | Command | Data |
|---|---|---|
| Power | `ka` | `01` on / `00` off / `ff` query |
| Input | `xb` | hex source code / `ff` query |
| Volume | `kf` | `00`–`64` (hex) / `ff` query |
| Audio Mute | `ke` | `00` mute / `01` unmute / `ff` query |
| Screen Mute | `kd` | `01` blank / `00` normal / `ff` query |
| Brightness | `kh` | `00`–`64` (hex) / `ff` query |
| Contrast | `kg` | `00`–`64` (hex) / `ff` query |
| Aspect Ratio | `kc` | `ff` query |
| Channel Up/Down | `mc` | `00` up / `01` down |
| Direct Tune | `ma` | major/minor in hex |

Response format: `[cmd2] [setID] OK[data]x` (success) or `[cmd2] [setID] NG[data]x` (error)

## Author

Dustin Bennett

## License

MIT
