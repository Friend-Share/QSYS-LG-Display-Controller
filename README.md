# LG Display Controller — Q-SYS Plugin

A Q-SYS scripted component plugin for controlling LG commercial displays via **RS-232C** or **TCP/IP** (port 9761), with optional **Wake-on-LAN** power-on support.

## Features

- **Dual Transport** — RS-232 (9600/8/N/1) or Ethernet TCP (port 9761)
- **Display Type Profiles** — Switch between *Commercial Signage* and *Pro:Centric* input source maps
- **Wake-on-LAN** — Optional WOL magic-packet power-on with configurable MAC address
- **Two-Tab Interface** — Display Control tab + Remote/Channel tab
- **Display Controls** — Power, audio mute, screen mute (blank), input source selection, volume, brightness, contrast, aspect ratio
- **Remote Control** — Full number pad (0–9), navigation d-pad (Up/Down/Left/Right/OK), Menu, Home, Back, Exit, Info, Settings
- **Channel Controls** — Channel up/down, direct tune (major/minor), and 6 favorite channel presets
- **Automatic Polling** — Configurable polling interval keeps all status indicators in sync
- **Startup Source** — Automatically switch to a configured input after power-on confirmation
- **Startup Mute** — Optionally mute audio on power-on
- **Multi-Display** — Configurable Set ID (0–99) for RS-232 daisy-chain environments
- **Stateful Buttons** — Power, source, mute, and screen mute buttons immediately reflect pressed state; text indicators update from actual display feedback
- **Pre-select Source** — Source buttons can be selected while the display is off; the chosen source is applied when power comes on

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
| **Display Type** | Dropdown | `Commercial Signage` | Switches input sources between Signage and Pro:Centric profiles |
| **Power On Method** | Dropdown | `Standard` | `Standard` (RS-232/TCP command) or `Wake on LAN` |
| **MAC Address** | Text | *(empty)* | Display MAC address for WOL (format: `AA:BB:CC:DD:EE:FF`) |
| **Startup Source** | Dropdown | `None` | Auto-switch to this input after power-on (only if no source was manually pre-selected) |
| **Startup Mute** | Dropdown | `Unmuted` | `Unmuted` or `Muted` — automatically mute audio on power-on |

### RS-232 Setup

1. Set **Connection Type** to `RS-232`
2. Wire the serial pin to your Q-SYS Core serial port
3. Cable: Null-modem / crossover RS-232 to the display's RS-232C port
4. Settings: **9600 baud, 8 data bits, No parity, 1 stop bit**

### TCP/IP Setup

1. Set **Connection Type** to `TCP`
2. Enter the display's **IP Address**
3. Ensure **IP Control** is enabled on the display (hidden installer menu)
4. The plugin connects on **port 9761**

### Wake-on-LAN Setup

1. Set **Power On Method** to `Wake on LAN`
2. Enter the display's **MAC Address** (e.g., `AB:CD:EF:12:34:56`)
3. Ensure the display and Q-SYS Core are on the same network/VLAN
4. Power On sends a WOL magic packet, then follows up with the standard command after 3 seconds

## Plugin Tabs

### Tab 1: Display Control

| Control | Function |
|---|---|
| **ON / OFF** | Power on/off (toggle buttons — stay lit to show current state) |
| **MUTE** | Toggle audio mute |
| **BLANK** | Toggle screen mute (video blanking) |
| **Source Buttons** | Select input (radio-group behavior, selectable while display is off) |
| **VOL +/−** | Adjust volume |
| **Bright +/−** | Adjust brightness |
| **Contrast +/−** | Adjust contrast |
| **Status Indicators** | Power, mute, source, volume, brightness, contrast, aspect ratio |
| **Connection LED** | Green = connected, Off = disconnected |

### Tab 2: Remote / Channel

| Control | Function |
|---|---|
| **Number Pad (0–9)** | Send number key presses (IR key codes) |
| **▲ ▼ ◄ ►** | Navigation d-pad |
| **OK** | Enter/confirm selection |
| **MENU** | Open display menu |
| **HOME** | Home screen |
| **BACK** | Go back |
| **EXIT** | Exit menu |
| **INFO** | Show info overlay |
| **SET** | Settings |
| **CH +/−** | Channel up/down |
| **Direct Tune** | Enter major/minor channel number and press TUNE |
| **FAV 1–6** | Recall a saved favorite channel |
| **SET (per fav)** | Store current channel to a favorite slot |

> **Note:** Channel controls require a display with a built-in RF tuner. Signage panels without tuners will not respond to channel commands.

## Button Behavior

- **Power ON/OFF** — Toggle buttons that stay lit to reflect current state. Cannot be untoggled by clicking again. If startup source is configured, the corresponding source button is pre-selected at init.
- **Source Buttons** — Radio group (one active at a time). Can be selected while display is off; the queued source is sent when power comes on. Cannot be deselected by clicking the active source again.
- **Mute / Screen Mute** — Toggle buttons that immediately reflect state. Text indicators update only from actual display feedback.

## Input Source Profiles

### Commercial Signage
HDMI 1–4, DisplayPort, DVI-D, USB, OPS/PC

### Pro:Centric
HDMI 1–4, Component, AV, USB, TV Tuner

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
| IR Key | `mc` | hex key code |
| Channel Up/Down | `mc` | `00` up / `01` down |
| Direct Tune | `ma` | major/minor in hex |

Response format: `[cmd2] [setID] OK[data]x` (success) or `[cmd2] [setID] NG[data]x` (error)

## Author
Dustin Bennett

