# Trackball Configuration Guide

## Overview

The split-ergo40pct-beta features an integrated PAW3222 optical sensor trackball on the right half, providing precise cursor control and scrolling functionality.

## Hardware Setup

### PAW3222 Sensor Specifications

- **Resolution**: 1600 CPI (configurable)
- **Interface**: SPI
- **Polling Rate**: Up to 2000 Hz
- **Power**: 3.3V operation

### Pin Connections (Right Half)

```
PAW3222 Pin → nRF52840 Pin → AKDK_D Pin
SCK         → P0.12        → D9
MOSI        → P1.09        → D10
MISO        → P1.09        → D10 (shared)
CS          → P0.13        → D8
IRQ         → P0.15        → D7
```

## Firmware Configuration

### Basic Settings

```c
trackball: trackball@0 {
    status = "okay";
    compatible = "pixart,paw3222";
    reg = <0>;
    spi-max-frequency = <2000000>;
    irq-gpios = <&gpio0 15 GPIO_ACTIVE_LOW>;

    rotation = <90>;                    // Rotate input 90 degrees

    // Normal operation
    res-cpi = <1600>;                   // Standard CPI

    // Precision mode (snipe)
    snipe-layers = <5 8 9>;             // Layers that activate snipe mode
    snipe-cpi = <400>;                  // Reduced CPI for precision
    snipe-divisor = <3>;                // Further divide movement by 3

    // Scroll mode
    scroll-layers = <6 7 8 9>;          // Layers that activate scrolling
    scroll-horizontal-layers = <7 9>;   // Horizontal scroll layers
};
```

### Acceleration Settings

```c
&pointer_accel {
    status = "okay";
    compatible = "zmk,input-processor-acceleration";

    track-remainders;                   // Enable sub-pixel precision
    sensitivity = <800>;                // Base sensitivity (0.8x)
    max-factor = <5000>;                // Maximum acceleration (5.0x)
    curve-type = <2>;                   // Acceleration curve type
    y-boost = <2500>;                   // Y-axis boost for widescreen
    speed-threshold = <200>;            // Speed to start acceleration
    speed-max = <4000>;                 // Speed for maximum acceleration
    min-factor = <800>;                 // Minimum sensitivity factor
    acceleration-exponent = <4>;        // Exponential curve strength
    sensor-dpi = <1500>;                // Sensor DPI for calculations
};
```

## Operating Modes

### 1. Normal Mode (Default)

- **CPI**: 1600
- **Acceleration**: Dynamic based on movement speed
- **Use**: General cursor movement

### 2. Snipe Mode (Precision)

- **Layers**: 5, 8, 9
- **CPI**: 400 (1/4 of normal)
- **Divisor**: 3 (further reduces sensitivity)
- **Effective sensitivity**: ~133 CPI equivalent
- **Use**: Precise targeting, detailed work

### 3. Scroll Mode

- **Layers**: 6, 7, 8, 9
- **Function**: Converts trackball movement to scroll wheel
- **Vertical scroll**: All scroll layers
- **Horizontal scroll**: Layers 7, 9
- **Use**: Document navigation, web browsing

## Layer Configuration

### Accessing Modes

#### Via Combos

```c
combos {
    compatible = "zmk,combos";

    Scroll {
        bindings = <&to 6>;             // Activate scroll mode
        key-positions = <50 44>;        // Combo keys
    };

    Scroll_snip {
        bindings = <&to 8>;             // Precision scroll mode
        key-positions = <50 46>;        // Combo keys
    };
};
```

#### Via Layer Keys

- **Mouse layer (4)**: Access via `&to 4` binding
- **Snipe layer (5)**: Toggle with `&tog 5`
- **Scroll layers**: Use combo or dedicated keys

### Layer Definitions

```c
// Mouse layer - basic mouse controls
Mouse {
    bindings = <
        // ... other keys ...
        &mkp LCLK  &mkp MCLK  &mkp RCLK  // Mouse buttons
        // ... other keys ...
    >;
};

// Snipe layer - precision mode active
Snipe {
    bindings = <
        // Trackball automatically switches to snipe mode
        &mkp LCLK  &mkp MCLK  &mkp RCLK  // Mouse buttons available
        // ... other keys ...
    >;
};
```

## Calibration and Tuning

### CPI Adjustment

Modify `res-cpi` value in trackball configuration:

- **Low sensitivity**: 800-1200 CPI
- **Medium sensitivity**: 1600 CPI (default)
- **High sensitivity**: 2400-3200 CPI

### Acceleration Tuning

Adjust acceleration parameters for different feel:

```c
// Gentle acceleration
sensitivity = <1000>;      // Higher base sensitivity
max-factor = <2000>;       // Lower max acceleration
curve-type = <1>;          // Linear curve

// Aggressive acceleration
sensitivity = <600>;       // Lower base sensitivity
max-factor = <8000>;       // Higher max acceleration
curve-type = <3>;          // Steep curve
```

### Snipe Mode Tuning

```c
snipe-cpi = <200>;         // Very precise (1/8 normal)
snipe-cpi = <400>;         // Precise (1/4 normal) - default
snipe-cpi = <800>;         // Moderate precision (1/2 normal)

snipe-divisor = <2>;       // Less reduction
snipe-divisor = <3>;       // Default reduction
snipe-divisor = <4>;       // More reduction
```

## Troubleshooting

### Trackball Not Detected

1. **Check SPI connections**:

   ```bash
   # Enable SPI debugging in prj.conf
   CONFIG_SPI_LOG_LEVEL_DBG=y
   ```

2. **Verify power supply**: Ensure 3.3V to sensor

3. **Test IRQ pin**: Should go low when trackball moves

### Erratic Movement

1. **Clean sensor lens**: Use compressed air
2. **Check for interference**: Keep away from wireless devices
3. **Adjust CPI**: Lower values may be more stable

### Acceleration Issues

1. **Disable acceleration temporarily**:

   ```c
   sensitivity = <1000>;
   max-factor = <1000>;  // No acceleration
   ```

2. **Check input transform**: Verify X/Y axis mapping

### Scroll Mode Problems

1. **Verify layer activation**: Check layer indicators
2. **Test scroll direction**: May need axis inversion
3. **Adjust scroll sensitivity**: Modify in scroll layer config

## Advanced Configuration

### Custom Input Processing

```c
trackball_listener: trackball_listener {
    compatible = "zmk,input-listener";
    device = <&trackball>;
    input-processors = <&pointer_accel>,
                      <&zip_xy_transform (INPUT_TRANSFORM_XY_SWAP |
                                        INPUT_TRANSFORM_X_INVERT)>;
};
```

### Transform Options

- `INPUT_TRANSFORM_XY_SWAP`: Swap X and Y axes
- `INPUT_TRANSFORM_X_INVERT`: Invert X axis
- `INPUT_TRANSFORM_Y_INVERT`: Invert Y axis

### Multiple Acceleration Profiles

Create different acceleration settings for different layers by defining multiple processors.

## Performance Optimization

### Polling Rate

```c
spi-max-frequency = <2000000>;  // 2MHz SPI clock
```

### Power Management

The PAW3222 automatically enters low-power mode when idle. No additional configuration needed.

### Latency Reduction

- Use higher SPI frequency (up to 2MHz)
- Enable `track-remainders` for sub-pixel precision
- Minimize acceleration processing overhead

## Maintenance

### Regular Cleaning

- Clean trackball surface weekly
- Use lint-free cloth
- Avoid liquids near sensor

### Firmware Updates

When updating trackball driver:

1. Check compatibility with current ZMK version
2. Review configuration changes
3. Test all modes after update

### Hardware Inspection

- Check solder joints on SPI connections
- Verify trackball mechanical mounting
- Inspect for dust accumulation
