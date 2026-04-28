# Q-SYS LG Display Controller

> ?? **This plugin is currently untested and in active development.** Features may change, and behavior on live hardware has not been fully validated. Use at your own risk and please report any issues.


This repository contains a Q-SYS plugin (`QSYS-LG_Display_Controller.qplug`) designed to control LG Commercial Displays over Ethernet (TCP) or RS-232 using the standard LG serial protocol.

## Features

- **Comprehensive Control**: Supports Power, Volume, Mute, Screen Mute, Brightness, and Contrast.
- **Input Switching**: Built-in logic to switch between standard LG input sources (HDMI, DP, DVI, Component, etc.).
- **Virtual Remote**: A dedicated tab simulating the physical IR remote, including a full Number Pad, Navigation D-Pad, Menu buttons, and direct channel tuning.
- **State-Aware UI**: The plugin buffers commands and utilizes toggle guards so the UI correctly reflects display feedback, ignoring errant user clicks if the hardware state differs.
- **Startup Logic**: Allows you to configure a "Startup Source" and "Startup Mute" state when the display powers on. If the display is off and a source is selected, it will queue the source command and execute it once the display has powered on.
- **Wake on LAN**: Built-in magic packet transmission for displays that support Network Standby.

## Connection Setup

### Ethernet (TCP/IP)
1. In the display menu, go to **Network → Control Port** and ensure it matches the plugin's default (typically **9761**).
2. Ensure **Wake On LAN** is enabled in the display settings to allow TCP control while the display is in standby.
3. In Q-SYS, set the Connection Type to **TCP** and enter the display's IP Address.

### RS-232
1. Connect a serial cable to the RS-232C port.
2. Ensure the display's control mode is set to RS-232.
3. In Q-SYS, set the Connection Type to **RS-232** and wire the `Serial Port` pin to your Q-SYS serial component. (Baud: 9600, Data: 8, Parity: None, Stop: 1).

### Set ID
LG displays use an ID for communication (1-99). Ensure the plugin's "Set ID" property matches the ID configured in the display. You can use ID `254` or `0` depending on the model for Broadcast.

## Installation

1. Download `QSYS-LG_Display_Controller.qplug`.
2. Move it to your Q-SYS Designer plugins directory:
   `Documents\QSC\Q-Sys Designer\Plugins`
3. Restart Q-SYS Designer to access the plugin in the schematic elements.

## Release Notes
- **v1.0.0.007**: Added toggle guards to prevent untoggling UI elements before hardware confirmation. Added robust startup source and mute buffering logic.
