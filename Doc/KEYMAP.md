# Keymap Configuration Guide

## Overview

The split-ergo40pct-beta uses a 10-layer keymap system designed for efficient typing and trackball control. This guide explains each layer's purpose and how to customize them.

## Layer Structure

### Layer Hierarchy

```
Layer 0: Base (QWERTY)           - Primary typing layer
Layer 1: Numbers                 - Number row and navigation
Layer 2: Function                - F-keys and system controls
Layer 3: Bluetooth               - BT management and numpad
Layer 4: Mouse                   - Mouse buttons and controls
Layer 5: Snipe                   - Precision trackball mode
Layer 6: Scroll                  - Vertical scrolling
Layer 7: Scroll_h                - Horizontal scrolling
Layer 8: Scroll_snipe            - Precision vertical scroll
Layer 9: Scroll_snipe_h          - Precision horizontal scroll
```

## Layer Details

### Layer 0: Base (QWERTY)

**Purpose**: Primary typing layer with standard QWERTY layout

**Key Features**:

- Standard QWERTY letter layout
- Modifier keys in home positions
- Layer access keys on thumbs
- Delete/Backspace on right thumb

**Layout**:

```
ESC  Q  W  E  R  T  [    ]    Y  U  I  O  P  DEL/BKSP
TAB  A  S  D  F  G  CAPS \    H  J  K  L  ;  '
SHFT Z  X  C  V  B            N  M  ,  .  /  SHFT
CTRL ALT CMD SPC L1 L2  L3   L4 ENT L0 L5 CMD ALT CTRL
```

**Access Methods**:

- Default layer (always accessible)
- Return via `&to 0` from other layers

### Layer 1: Numbers

**Purpose**: Number row and basic navigation

**Key Features**:

- Number row (1-9, 0)
- Arrow keys on right side
- Minus and equals symbols
- Quick app switching (Cmd+Q, Cmd+W)

**Layout**:

```
---  1  2  3  4  5  -    =    6  7  8  9  0  ---
---  -  -  -  -  -  ---  `    -  -  -  -  -  ---
---  ⌘Q ⌘W -  -  -            -  -  -  -  ↑  ---
---  -- -- -- -- --  ---  --- -- -- -- ←  ↓  →
```

**Access**: Hold or tap key bound to `&to 1`

### Layer 2: Function Keys

**Purpose**: Function keys and system controls

**Key Features**:

- Complete F1-F12 row
- System function access
- Media controls (if configured)

**Layout**:

```
--- F1 F2 F3 F4 F5 F6   F7  F8 F9 F10 F11 F12 ---
--- -- -- -- -- -- --  --  -- -- --  --  --  ---
--- -- -- -- -- --         -- -- --  --  --  ---
--- -- -- -- -- -- --- --- -- -- --  --  --  ---
```

**Access**: Hold or tap key bound to `&to 2`

### Layer 3: Bluetooth & Numpad

**Purpose**: Bluetooth management and numeric keypad

**Key Features**:

- Bluetooth profile selection (0-4)
- Bluetooth clear and disconnect
- Numeric keypad on right side
- System reset functions

**Layout**:

```
--- BT0 BT1 BT2 BT3 BT4 PRV CLR CLRA -- -- 7 8 9
--- DC0 DC1 DC2 DC3 DC4 NXT --  --   -- -- 4 5 6
--- --  --  --  --  --              -- -- 1 2 3
--- --  --  --- --- --- --- ---     -- -- 0 . DEL
```

**Special Functions**:

- `BT0-BT4`: Select Bluetooth profile
- `DC0-DC4`: Disconnect specific profile
- `PRV/NXT`: Previous/Next profile
- `CLR`: Clear current profile
- `CLRA`: Clear all profiles

**Access**: Hold or tap key bound to `&to 3`

### Layer 4: Mouse

**Purpose**: Basic mouse button controls

**Key Features**:

- Left, middle, right mouse buttons
- Boot/reset functionality
- Compatible with trackball movement

**Layout**:

```
--- -- -- --  --  -- BOOT -- --   --   --   -- -- ---
--- -- -- --  --  -- --   -- LCLK MCLK RCLK -- -- ---
--- -- -- --  --  --        --   --   --   -- -- ---
--- -- -- --- --- -- ---  --- --   --   --   -- -- ---
```

**Special Functions**:

- `BOOT`: Bootloader mode (hold for reset)
- `LCLK/MCLK/RCLK`: Mouse buttons

**Access**: Hold or tap key bound to `&to 4`

### Layer 5: Snipe (Precision Mode)

**Purpose**: Precision trackball control

**Key Features**:

- Trackball automatically switches to snipe mode (400 CPI)
- Mouse buttons available
- Reduced sensitivity for precise work

**Behavior**:

- Trackball CPI reduced to 400
- Movement divided by snipe-divisor (3)
- Effective sensitivity: ~133 CPI equivalent

**Access**: Toggle with `&tog 5` or combo keys

### Layers 6-9: Scroll Modes

**Purpose**: Convert trackball movement to scrolling

#### Layer 6: Scroll (Vertical)

- Trackball Y-axis → Vertical scroll
- Standard scroll speed

#### Layer 7: Scroll_h (Horizontal)

- Trackball X-axis → Horizontal scroll
- Momentary layer via `&mo 7`

#### Layer 8: Scroll_snipe (Precision Vertical)

- Precision vertical scrolling
- Reduced scroll speed for fine control

#### Layer 9: Scroll_snipe_h (Precision Horizontal)

- Precision horizontal scrolling
- Momentary layer via `&mo 9`

**Access Methods**:

- Combo: Keys 50+44 → Layer 6
- Combo: Keys 50+46 → Layer 8
- Direct layer switches

## Combo System

### Defined Combos

```c
combos {
    compatible = "zmk,combos";

    Scroll {
        bindings = <&to 6>;           // Activate scroll mode
        key-positions = <50 44>;      // Right thumb + specific key
    };

    Scroll_snip {
        bindings = <&to 8>;           // Precision scroll mode
        key-positions = <50 46>;      // Right thumb + specific key
    };
};
```

### Key Position Reference

Key positions are numbered 0-53 from left to right, top to bottom:

```
Row 0:  0  1  2  3  4  5  6    7  8  9 10 11 12 13
Row 1: 14 15 16 17 18 19 20   21 22 23 24 25 26 27
Row 2: 28 29 30 31 32 33      34 35 36 37 38 39
Row 3: 40 41 42 43 44 45 46   47 48 49 50 51 52 53
```

## Customization Guide

### Adding New Layers

1. **Define layer in keymap**:

   ```c
   new_layer {
       bindings = <
           // 54 key bindings here
       >;
   };
   ```

2. **Add access method**:
   ```c
   &to 10  // Switch to layer 10
   &mo 10  // Momentary layer 10
   &tog 10 // Toggle layer 10
   ```

### Modifying Existing Layers

1. **Identify key position** using the reference above
2. **Replace binding** in the layer definition
3. **Test thoroughly** before committing changes

### Common Key Bindings

```c
// Basic keys
&kp A              // Letter A
&kp NUMBER_1       // Number 1
&kp F1             // Function key F1

// Modifiers
&kp LEFT_SHIFT     // Left shift
&mt LSHIFT A       // Mod-tap: Shift when held, A when tapped

// Layer controls
&to 1              // Switch to layer 1
&mo 2              // Momentary layer 2 (while held)
&tog 3             // Toggle layer 3 on/off

// Mouse
&mkp LCLK          // Left mouse click
&mkp RCLK          // Right mouse click

// Bluetooth
&bt BT_SEL 0       // Select BT profile 0
&bt BT_CLR         // Clear current BT profile

// Special
&trans             // Transparent (use lower layer)
&none              // No operation
```

### Trackball Layer Configuration

To add trackball functionality to a layer:

1. **For snipe mode**: Add layer number to `snipe-layers`
2. **For scroll mode**: Add layer number to `scroll-layers`
3. **For horizontal scroll**: Add to `scroll-horizontal-layers`

Example:

```c
trackball: trackball@0 {
    snipe-layers = <5 8 9 10>;        // Add layer 10
    scroll-layers = <6 7 8 9 11>;     // Add layer 11
    scroll-horizontal-layers = <7 9>; // Horizontal scroll layers
};
```

## Best Practices

### Layer Design

1. **Keep base layer clean**: Only essential keys
2. **Group related functions**: Numbers together, F-keys together
3. **Maintain muscle memory**: Keep common keys in similar positions
4. **Use transparent keys**: Let lower layers show through

### Trackball Integration

1. **Test all modes**: Verify normal, snipe, and scroll modes
2. **Logical layer progression**: Related functions on adjacent layers
3. **Easy access**: Important modes should be easily reachable

### Combo Design

1. **Comfortable positions**: Use keys that are easy to press together
2. **Avoid conflicts**: Don't overlap with common typing patterns
3. **Logical grouping**: Related functions use similar combos

## Troubleshooting

### Layer Not Activating

1. **Check key binding**: Verify `&to X` or `&mo X` syntax
2. **Test key position**: Use a simple binding to test the key
3. **Check for conflicts**: Ensure no overlapping layer locks

### Trackball Mode Issues

1. **Verify layer numbers**: Check trackball configuration
2. **Test layer activation**: Confirm layer is actually active
3. **Check hardware**: Ensure trackball is functioning

### Combo Not Working

1. **Verify key positions**: Double-check position numbers
2. **Test timing**: Combos require simultaneous presses
3. **Check for conflicts**: Ensure positions don't conflict with typing

## Advanced Features

### Conditional Layers

Use layer conditions for complex behaviors:

```c
conditional_layers {
    compatible = "zmk,conditional-layers";
    tri_layer {
        if-layers = <1 2>;    // If layers 1 AND 2 are active
        then-layer = <3>;     // Activate layer 3
    };
};
```

### Tap Dance

Create multi-function keys:

```c
tap_dance_0: tap_dance_0 {
    compatible = "zmk,behavior-tap-dance";
    #binding-cells = <0>;
    tapping-term-ms = <200>;
    bindings = <&kp N1>, <&kp N2>, <&kp N3>;  // 1, 2, or 3 taps
};
```

### Hold-Tap Behaviors

Customize modifier behavior:

```c
ht_hp: hold_tap_hold_preferred {
    compatible = "zmk,behavior-hold-tap";
    #binding-cells = <2>;
    tapping-term-ms = <200>;
    quick-tap-ms = <0>;
    flavor = "hold-preferred";
    bindings = <&kp>, <&kp>;
};
```

## Testing Your Keymap

### Systematic Testing

1. **Test each layer**: Verify all keys work as expected
2. **Test layer transitions**: Ensure smooth switching
3. **Test combos**: Verify all combinations work
4. **Test trackball modes**: Check all pointing modes

### Debug Tools

1. **ZMK Studio**: Visual keymap editor and tester
2. **USB logging**: Enable logging for debugging
3. **Key position tester**: Use simple bindings to identify positions

### Common Issues

- **Wrong key positions**: Use the reference grid above
- **Syntax errors**: Check ZMK documentation for correct syntax
- **Layer conflicts**: Ensure logical layer hierarchy
- **Trackball configuration**: Verify layer numbers match
