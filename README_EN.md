# Eyelash Sofle - ZMK Firmware

- [Chinese](README.md)
- [English](README_EN.md)

## Features

- Split wireless keyboard (nRF52840, Bluetooth 5.2)
- Nice View OLED display (left half)
- Rotary encoder (left half)
- Mouse movement and scrolling
- ZMK Studio support
- **RGB underglow with One Piece themed animations** (via [zmk-rgb-fx](https://github.com/crystalplanet/zmk-rgb-fx))

### RGB Themes

| Key | Theme | Effect |
|-----|-------|--------|
| N6 (Layer 1) | **Op Sunny** | Gold-to-ocean animated gradient (Thousand Sunny) |
| N7 (Layer 1) | **Haki** | Dark crimson base + ripple on keypress (Armament Haki) |
| N8 (Layer 1) | **Gear 5** | White/purple pulse + sparkle overlay (Nika) |

Additional controls on Layer 1: toggle on/off, next/prev theme, brighten/dim.

## Update List

- 2024/10/24: Modified power supply mode to reduce power consumption. Fixed RGB auto-off.
- 2024/12/21: Added ZMK Studio support (flash left hand only).
- 2025/8/22: Added soft off (hold Q+S+Z for 2s). Removed right-side GIF to reduce power.

> If your Sofle was updated before 2025/8/22, please update to the latest firmware.

## Sofle Keymap

![Sofle Keymap](keymap-drawer/eyelash_sofle.svg)

## Local Build Setup

### Prerequisites

- macOS (Apple Silicon or Intel) or Linux
- Homebrew (macOS) or equivalent package manager

### 1. Install build tools

```bash
# macOS
brew install cmake ninja dtc gperf ccache wget

# Install Python packages
pip3 install --user west
pip3 install --user -r zephyr/scripts/requirements.txt
```

Make sure `west` is on your PATH:
```bash
export PATH="$HOME/Library/Python/3.9/bin:$PATH"
```

### 2. Install Zephyr SDK

```bash
# Download (macOS ARM)
cd /tmp
curl -L -o zephyr-sdk-0.16.8_macos-aarch64.tar.xz \
  https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.16.8/zephyr-sdk-0.16.8_macos-aarch64.tar.xz

# For macOS Intel, use: zephyr-sdk-0.16.8_macos-x86_64.tar.xz
# For Linux x86_64, use: zephyr-sdk-0.16.8_linux-x86_64.tar.xz

# Extract and install
tar xf zephyr-sdk-0.16.8_macos-aarch64.tar.xz
mv zephyr-sdk-0.16.8 ~/zephyr-sdk-0.16.8
cd ~/zephyr-sdk-0.16.8
./setup.sh -t arm-zephyr-eabi
```

### 3. Initialize the workspace

```bash
cd /path/to/zmk-sofle
west init -l config/
west update
```

### 4. Set environment variables

Add these to your shell profile (`.zshrc` / `.bashrc`):
```bash
export PATH="$HOME/Library/Python/3.9/bin:$PATH"
export ZEPHYR_SDK_INSTALL_DIR=~/zephyr-sdk-0.16.8
export ZEPHYR_BASE=/path/to/zmk-sofle/zephyr
export CMAKE_PREFIX_PATH=/path/to/zmk-sofle/zephyr/share/zephyr-package/cmake
```

### 5. Build firmware

```bash
# Left half (standard)
west build -s zmk/app -b eyelash_sofle_left -p always -- \
  -DZMK_CONFIG="$(pwd)/config" -DSHIELD=nice_view

# Right half
west build -s zmk/app -b eyelash_sofle_right -d build_right -p always -- \
  -DZMK_CONFIG="$(pwd)/config" -DSHIELD=nice_view

# Left half with ZMK Studio
west build -s zmk/app -b eyelash_sofle_left -d build_studio -p always -- \
  -DZMK_CONFIG="$(pwd)/config" -DSHIELD=nice_view \
  -DCONFIG_ZMK_STUDIO=y -DCONFIG_ZMK_STUDIO_LOCKING=n \
  -DSNIPPET=studio-rpc-usb-uart
```

Output: `build/zephyr/zmk.uf2` (or `build_right/`, `build_studio/`)

### 6. Flash firmware

1. Put the keyboard half into bootloader mode (double-tap reset button)
2. A USB mass storage device will appear
3. Copy the `.uf2` file to it
4. The keyboard will reboot automatically

## Contact

For 3D printed model files or hardware issues: [380465425@qq.com](mailto:380465425@qq.com)
