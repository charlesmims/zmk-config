# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a ZMK (Zephyr Mechanical Keyboard) firmware configuration repository for a Corne split keyboard with nice!view displays, running on nice!nano v2 controllers.

## Build System

Builds are automated through GitHub Actions. The firmware is built remotely - there is no local build process needed.

- **Build trigger**: Commits pushed to GitHub automatically trigger firmware builds via `.github/workflows/build.yml`
- **Build configuration**: `build.yaml` defines the board and shield combinations for the GitHub Actions matrix
- **Artifacts**: After a successful build, download `.uf2` firmware files from the GitHub Actions run

Current build configuration builds two firmware files:
- Left half: `nice_nano_v2` + `corne_left` + `nice_view_adapter` + `nice_view`
- Right half: `nice_nano_v2` + `corne_right` + `nice_view_adapter` + `nice_view`

## Architecture

### Configuration Files

The primary configuration files are in the `config/` directory:

- **`corne.keymap`**: Main keymap definition file containing:
  - Custom behaviors (homerow mods, alt mods, backspace/delete mod-morph)
  - Layer definitions (base, lower, raise)
  - Key bindings using ZMK devicetree syntax

- **`corne.conf`**: Keyboard configuration settings including:
  - Bluetooth TX power settings
  - Debounce timing (1ms press, 7ms release for eager debouncing)
  - Optional features (sleep mode, USB logging - currently commented out)

- **`west.yml`**: West manifest that pulls in the main ZMK firmware from `zmkfirmware/zmk` on GitHub

### Keymap Structure

The keymap uses a Colemak-based layout with three layers:

1. **Base layer (layer 0)**: Main typing layer with homerow mods
   - Homerow mods (`hm` behavior): Quick tap-preferred with 200ms tapping term
   - Alt mods (`am` behavior): Longer 500ms tapping term for Alt-based shortcuts
   - Layer-tap keys for accessing other layers

2. **Lower layer (layer 1)**: Numbers and navigation
   - Number row (1-0)
   - Arrow keys and navigation (Home, End, PgUp, PgDn)
   - Bluetooth controls (clear, profile selection)

3. **Raise layer (layer 2)**: Symbols and media
   - Symbol keys (!, @, #, etc.)
   - Media controls (volume, play/pause, prev/next)
   - Brightness controls

### Custom Behaviors

- **`hm` (homerow_mods)**: Tap-preferred hold-tap with 200ms tapping term, 100ms quick-tap, 200ms prior-idle requirement
- **`am` (alt_mods)**: Tap-preferred hold-tap with 500ms tapping term for Alt key combinations
- **`bkspdel`**: Mod-morph that acts as Backspace normally, Delete when Shift is held

### Directory Structure

- `.github/workflows/`: GitHub Actions build workflow
- `boards/shields/`: Custom shield definitions (currently empty with just .gitkeep)
- `config/`: All keyboard configuration and keymap files
- `zephyr/`: Zephyr module configuration (sets board_root)
- `build.yaml`: Build matrix configuration

## Modifying the Keymap

When editing `config/corne.keymap`:

1. Use ZMK devicetree syntax: `&kp KEY`, `&lt LAYER KEY`, `&mt MOD KEY`, etc.
2. Key positions match the Corne's 42-key layout (3x6 + 3 thumb keys per side)
3. Layers are zero-indexed
4. Custom behaviors are defined in the `behaviors` block before the keymap
5. Test changes by committing and checking the GitHub Actions build

## Bluetooth Configuration

This configuration uses increased BT TX power (`CONFIG_BT_CTLR_TX_PWR_PLUS_8=y`) for extended wireless range. Bluetooth profile management is available on the lower layer.
