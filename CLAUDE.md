# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a ZMK firmware configuration repository for a custom split keyboard called "Topey" based on the Skeletyl layout. The keyboard uses nice_nano_v2 controllers and features:
- Custom keymap with home row modifiers
- Layer-based navigation (QWERTY, Arrows, Numbers, Symbols, Mouse)
- Combo keys for frequently used shortcuts
- ZMK pointing device support for mouse layer

## Build System

Builds are handled automatically by GitHub Actions when code is pushed. The workflow uses ZMK's official build-user-config.yml workflow.

**Build configuration:** `build.yaml` defines the board/shield combinations:
- `nice_nano_v2` + `topey_left`
- `nice_nano_v2` + `topey_right`
- `nice_nano_v2` + `settings_reset`

The builds produce `.uf2` firmware files as GitHub Action artifacts.

## Repository Structure

```
config/
├── topey.keymap          # Main keymap (symlinked/copied from boards/shields/topey/)
├── topey.conf            # Keyboard configuration (BLE, pointing device settings)
├── topey.json            # Layout visualization (keymap-drawer format)
├── west.yml              # West manifest pointing to zmkfirmware/zmk main branch
└── boards/shields/topey/ # Shield definition
    ├── topey.keymap      # Actual keymap source
    ├── topey.dtsi        # Common device tree include
    ├── topey_left.overlay
    ├── topey_right.overlay
    ├── Kconfig.shield
    └── Kconfig.defconfig
```

## Keymap Architecture

The keymap uses ZMK's devicetree syntax and is structured around:

**Layer definitions:**
- `QWE (0)`: Default QWERTY with home row mods (GACS mirrored)
- `ARR (1)`: Arrow navigation, media controls, Bluetooth
- `NUM (2)`: Numbers, function keys, common symbols
- `SYM (3)`: Shifted number row symbols, brackets
- `MOU (4)`: Mouse movement and scrolling via pointing API

**Key behaviors:**
- `qt` (quick-tap): Custom hold-tap behavior for home row mods (250ms tapping-term, 200ms quick-tap)
- Combos: D+F=Tab, L+;=Enter, ,+.=>, F+J=CapsWord, Space+Backspace=Mouse toggle
- Macros: `fat_arrow` (=>), `num_gui` (momentary NUM layer + GUI modifier)

**Configuration notes:**
- `caps_word` configured to continue with underscores, hyphens, and numbers
- Pointing device support enabled via `CONFIG_ZMK_POINTING=y` in topey.conf
- BLE experimental features enabled for enhanced Bluetooth functionality

## Editing Keymaps

1. The primary keymap file is `/config/boards/shields/topey/topey.keymap`
2. There's also a copy at `/config/topey.keymap` (check which is canonical)
3. Layer indices are defined as preprocessor macros at the top (QWE=0, ARR=1, etc.)
4. Each key binding follows ZMK behavior syntax: `&behavior parameters`
5. Reference `keyboard-layout.md` for the logical layout description

## West Manifest

The `config/west.yml` pulls the main ZMK firmware repository. To update ZMK version, modify the `revision` field in west.yml.
