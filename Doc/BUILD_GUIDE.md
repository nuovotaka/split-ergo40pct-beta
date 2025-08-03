# Build and Setup Guide

## Prerequisites

### Required Software

- **Git**: Version control system
- **West**: Zephyr's meta-tool for managing repositories
- **Python 3.8+**: Required for West and build tools
- **CMake 3.20+**: Build system
- **Ninja**: Build tool (recommended)
- **ARM GCC toolchain**: For nRF52840 compilation

### Development Environment Setup

#### Option 1: Using ZMK Docker (Recommended)

```bash
# Pull the ZMK Docker image
docker pull zmkfirmware/zmk-build-arm:stable

# Create alias for easy use
alias zmk-docker="docker run --rm -it --name zmk \
  -v $(pwd):/app \
  -v /dev:/dev --privileged \
  -w /app zmkfirmware/zmk-build-arm:stable"
```

#### Option 2: Native Installation

1. **Install Python and pip**:

   ```bash
   # macOS
   brew install python3

   # Ubuntu/Debian
   sudo apt install python3 python3-pip
   ```

2. **Install West**:

   ```bash
   pip3 install --user west
   ```

3. **Install ARM toolchain**:

   ```bash
   # macOS
   brew install --cask gcc-arm-embedded

   # Ubuntu/Debian
   sudo apt install gcc-arm-none-eabi
   ```

4. **Install build tools**:

   ```bash
   # macOS
   brew install cmake ninja

   # Ubuntu/Debian
   sudo apt install cmake ninja-build
   ```

## Repository Setup

### 1. Clone Repository

```bash
git clone https://github.com/nuovotaka/split-ergo40pct-beta.git
cd split-ergo40pct-beta
```

### 2. Initialize West Workspace

```bash
# Initialize west with this repository's config
west init -l config

# Update all dependencies
west update
```

This will download:

- **ZMK firmware** (v0.3.0)
- **zmk-pointing-acceleration-alpha**: Trackball acceleration
- **zmk-driver-paw3222-alpha**: PAW3222 sensor driver

### 3. Verify Setup

```bash
# Check west status
west status

# List available boards
west boards | grep akdk_bt1

# List available shields
west shields | grep split_ergo40pct
```

## Building Firmware

### Using Docker (Recommended)

```bash
# Build left half
zmk-docker west build -p -b akdk_bt1 -- -DSHIELD=split_ergo40pct_left -DCONFIG_ZMK_STUDIO=y

# Build right half
zmk-docker west build -p -b akdk_bt1 -- -DSHIELD=split_ergo40pct_right -DCONFIG_ZMK_STUDIO=y

# Build settings reset
zmk-docker west build -p -b akdk_bt1 -- -DSHIELD=settings_reset
```

### Using Native Tools

```bash
# Build left half
west build -p -b akdk_bt1 -- -DSHIELD=split_ergo40pct_left -DCONFIG_ZMK_STUDIO=y

# Build right half
west build -p -b akdk_bt1 -- -DSHIELD=split_ergo40pct_right -DCONFIG_ZMK_STUDIO=y

# Build settings reset
west build -p -b akdk_bt1 -- -DSHIELD=settings_reset
```

### Build Options Explained

- `-p`: Pristine build (clean first)
- `-b akdk_bt1`: Target board
- `-DSHIELD=split_ergo40pct_left`: Shield configuration
- `-DCONFIG_ZMK_STUDIO=y`: Enable ZMK Studio support

### Build Outputs

Successful builds create `.uf2` files in `build/zephyr/`:

- `zmk.uf2`: Main firmware file
- Copy this file to the controller in bootloader mode

## Flashing Firmware

### 1. Enter Bootloader Mode

**Method 1: Reset Button**

- Double-tap the reset button on the controller
- Controller appears as USB mass storage device

**Method 2: Key Combination**

- Hold bootloader combo keys (if configured)
- Controller enters bootloader mode

### 2. Flash Firmware

```bash
# Controller appears as mounted drive (e.g., NICENANO)
cp build/zephyr/zmk.uf2 /Volumes/NICENANO/

# Or drag and drop the .uf2 file to the mounted drive
```

### 3. Verify Flash

- Controller automatically reboots after flashing
- LED should indicate successful boot
- Test basic key functionality

## Initial Configuration

### 1. Pair Split Halves

1. **Flash both halves** with respective firmware
2. **Power on both halves**
3. **Wait for pairing**: LEDs should indicate connection
4. **Test split functionality**: Type on both halves

### 2. Bluetooth Pairing

1. **Enter pairing mode**: Use BT layer (Layer 3)
2. **Select profile**: Press BT0-BT4 keys
3. **Pair with device**: Find "split_ergo40pct" in BT settings
4. **Test connection**: Verify typing works

### 3. Trackball Calibration

1. **Test basic movement**: Move trackball, verify cursor
2. **Test click functions**: Use mouse layer (Layer 4)
3. **Test scroll modes**: Use combos to activate scroll
4. **Adjust sensitivity**: Modify CPI settings if needed

## Customization

### Modifying Keymap

1. **Edit keymap file**:

   ```bash
   nano config/keymap.keymap
   ```

2. **Rebuild firmware**:

   ```bash
   west build -p -b akdk_bt1 -- -DSHIELD=split_ergo40pct_left
   ```

3. **Flash updated firmware**

### Trackball Settings

Edit `boards/shields/split_ergo40pct/split_ergo40pct_right.overlay`:

```c
trackball: trackball@0 {
    res-cpi = <800>;          // Adjust base sensitivity
    snipe-cpi = <200>;        // Adjust precision sensitivity
    rotation = <90>;          // Adjust rotation (0, 90, 180, 270)
};
```

### Acceleration Tuning

Modify `&pointer_accel` section:

```c
&pointer_accel {
    sensitivity = <1000>;     // Base sensitivity
    max-factor = <3000>;      // Maximum acceleration
    curve-type = <1>;         // Acceleration curve
};
```

## Troubleshooting

### Build Errors

**"Board not found"**:

```bash
# Verify board exists
west boards | grep akdk_bt1

# Check board definition
ls boards/arm/akdk_bt1/
```

**"Shield not found"**:

```bash
# Verify shield exists
west shields | grep split_ergo40pct

# Check shield definition
ls boards/shields/split_ergo40pct/
```

**"Module not found"**:

```bash
# Update dependencies
west update

# Check module status
west status
```

**Compilation errors**:

```bash
# Clean build directory
rm -rf build/

# Pristine build
west build -p -b akdk_bt1 -- -DSHIELD=split_ergo40pct_left
```

### Flashing Issues

**Controller not detected**:

1. Check USB cable connection
2. Try different USB port
3. Verify bootloader mode (double-tap reset)
4. Check for driver issues (Windows)

**Flash fails**:

1. Ensure sufficient space on controller
2. Try different .uf2 file
3. Reset controller and retry
4. Check file permissions

### Runtime Issues

**Keys not working**:

1. Check keymap syntax
2. Verify GPIO connections
3. Test with simple keymap
4. Check for hardware issues

**Split halves not connecting**:

1. Re-flash both halves
2. Clear Bluetooth bonds
3. Check battery levels
4. Verify pairing process

**Trackball not working**:

1. Check SPI connections
2. Verify sensor power
3. Test with basic movement
4. Check firmware includes driver

### Debug Tools

**Enable logging**:
Add to `config/split_ergo40pct_left.conf`:

```
CONFIG_ZMK_USB_LOGGING=y
CONFIG_LOG_LEVEL_DBG=y
```

**View logs**:

```bash
# Connect via USB and monitor serial output
screen /dev/ttyACM0 115200
```

**Test individual components**:
Create minimal test keymaps to isolate issues.

## Advanced Configuration

### Custom Board Definition

To create a custom board variant:

1. **Copy existing board**:

   ```bash
   cp -r boards/arm/akdk_bt1 boards/arm/my_board
   ```

2. **Modify board files**:

   - Update `my_board.dts`
   - Modify `Kconfig.board`
   - Adjust GPIO mappings

3. **Build with custom board**:
   ```bash
   west build -b my_board -- -DSHIELD=split_ergo40pct_left
   ```

### Multiple Keymap Variants

Create different keymap configurations:

1. **Create keymap variants**:

   ```bash
   cp config/keymap.keymap config/gaming.keymap
   cp config/keymap.keymap config/office.keymap
   ```

2. **Build specific variant**:
   ```bash
   west build -b akdk_bt1 -- -DSHIELD=split_ergo40pct_left -DZMK_CONFIG="$(pwd)/config/gaming"
   ```

### Continuous Integration

Set up automated builds using GitHub Actions:

```yaml
# .github/workflows/build.yml
name: Build ZMK firmware
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    container:
      image: zmkfirmware/zmk-build-arm:stable
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Cache west modules
        uses: actions/cache@v2
        with:
          path: |
            modules/
            tools/
            zephyr/
            bootloader/
            zmk/
          key: ${{ runner.os }}-build-${{ hashFiles('**/west.yml') }}
      - name: West Init
        run: west init -l config
      - name: West Update
        run: west update
      - name: Build Left
        run: west build -s zmk/app -b akdk_bt1 -- -DSHIELD=split_ergo40pct_left
      - name: Build Right
        run: west build -s zmk/app -b akdk_bt1 -- -DSHIELD=split_ergo40pct_right
```

## Maintenance

### Updating Dependencies

```bash
# Update to latest ZMK
west update

# Check for conflicts
west status

# Rebuild after updates
west build -p
```

### Backup Configuration

```bash
# Backup your customizations
tar -czf my-keyboard-config.tar.gz config/ boards/shields/split_ergo40pct/
```

### Version Control

```bash
# Track your changes
git add config/keymap.keymap
git commit -m "Update keymap for better ergonomics"

# Create branches for experiments
git checkout -b experimental-layout
```

## Performance Optimization

### Build Speed

- Use `-j$(nproc)` for parallel builds
- Enable ccache for faster rebuilds
- Use SSD storage for build directory

### Runtime Performance

- Optimize keymap for fewer layers
- Reduce combo timeout values
- Minimize complex behaviors

### Power Optimization

- Reduce RGB brightness
- Lower BLE advertising interval
- Use sleep modes effectively

## Getting Help

### Documentation

- **ZMK Docs**: https://zmk.dev/docs
- **Zephyr Docs**: https://docs.zephyrproject.org
- **nRF52840 Reference**: Nordic Semiconductor documentation

### Community Support

- **ZMK Discord**: Real-time help and discussion
- **GitHub Issues**: Bug reports and feature requests
- **Reddit r/ErgoMechKeyboards**: Community discussions

### Hardware Support

- Check PCB documentation
- Verify component specifications
- Test with multimeter if needed
