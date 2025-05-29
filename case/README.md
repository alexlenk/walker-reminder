# Walker Reminder - 3D Printable Case

This directory contains the OpenSCAD design files for the Walker Reminder device case. The case is designed to house a Seeed Studio XIAO ESP32C3 board, a 3000mAh Li-ion battery pack, and a passive buzzer.

## Design Overview

The case is created using the [YAPP (Yet Another Parametric Projectbox) generator](https://github.com/mrWheel/YAPP_Box), which allows for easy customization of the design.

### Case Dimensions

- Internal dimensions: 50mm × 45mm × 25mm
- Wall thickness: 2.0mm
- Base plane thickness: 1.6mm
- Lid plane thickness: 1.6mm
- Rounded corners: 5mm radius

### Features

- Mounting holes on both sides for attaching to a walker
- PCB standoffs for the Seeed Studio XIAO ESP32C3 board
- Additional support near the USB port to prevent movement when connecting cables
- USB-C cutout for programming and power
- Battery pack mounting point for 3000mAh Li-ion battery
- "Walker Reminder" label on the lid

## Files

- `YAPPgenerator_v3.scad` - The YAPP library file
- `walker_case.scad` - The case design file

## Printing Instructions

1. Open `walker_case.scad` in OpenSCAD
2. Render the design (F6)
3. Export as STL (F7)
4. Print with the following recommended settings:
   - Layer height: 0.2mm
   - Infill: 20%
   - Supports: Not required
   - Material: PLA or PETG

## Assembly

1. Place the Seeed Studio XIAO ESP32C3 board on the standoffs in the base
2. Secure the 3000mAh Li-ion battery pack to the mounting point
3. Connect the buzzer and place it in the designated area
4. Close the lid and secure it with the snap joints

## Customization

The case design can be easily modified by adjusting parameters in the `walker_case.scad` file:

- Change dimensions by modifying `pcbLength`, `pcbWidth`, and padding values
- Adjust wall heights with `baseWallHeight` and `lidWallHeight`
- Add cutouts for additional components as needed

### Adding a Buzzer Cutout

To add a cutout for the buzzer, uncomment or add the following code:

```scad
// Cutout for passive buzzer on the lid
cutoutsLid = [
  [30, 25, 12, 12, 6, yappCircle, 0, 0]  // Buzzer cutout
];
```

## Mounting Options

The case includes mounting holes on both sides for attaching to a walker. The mounting holes are designed for M3 or M4 screws.