# Walker Reminder - ESPHome Configuration

This directory contains the ESPHome configuration for the Walker Reminder device, which helps disabled people remember to take their walker by alerting them when they move too far away from it.

## Hardware Requirements

- Seeed Studio XIAO ESP32C3 (with built-in battery management)
- Passive buzzer (3V)
- 3000mAh Li-ion battery pack
- NPN transistor (e.g., 2N2222 or BC547)
- 1kΩ resistor
- Momentary push button (for config mode)
- iBeacon wristband

## Pin Configuration

- **GPIO1**: Battery voltage monitoring (uses built-in battery management)
- **GPIO9**: Configuration button input (with pull-up)
- **GPIO10**: Buzzer control (via transistor)

## Wiring Diagram

### Battery and Button Connections
```
+----------------+                  +-----------------+
|                |                  |                 |
| 3000mAh        |   Red (+)        |                 |
| Li-ion Battery |------------------|  BAT+           |
| Pack           |                  |                 |
|                |   Black (-)      |                 |
|                |------------------|  BAT-           |
+----------------+                  |                 |
                                    |                 |
                                    |  Seeed Studio   |
+----------------+                  |  XIAO ESP32C3   |
|                |   Signal         |                 |
| Config Button  |------------------|  GPIO9          |
|                |   GND            |                 |
|                |------------------|  GND            |
+----------------+                  |                 |
                                    |                 |
                                    |  GPIO10         |
                                    |                 |
                                    |  GND            |
                                    |                 |
                                    +-----------------+
```

### Buzzer and Transistor Connections
```
                     VCC/3.3V
                        |
                        |
                        v
+----------------+      |
|                |      |
| Passive Buzzer |<-----+ Positive terminal to VCC/3.3V
| (3V)           |      
|                |      
|                |------+ Negative terminal to Collector
+----------------+      |
                        |
                        v
                    Collector
                        |
                   +----+----+
                   |    |    |
                   |    |    |
          Base ----+    |    |
                   |  2N2222 |
                   |    |    |
                   |    |    |
                   +----+----+
                        |
                        |
                        v Emitter
                        |
                        |
                        |
                        v
                       GND
                        ^
                        |
                        |
            1kΩ         |
        GPIO10-/\/\/\---+
```

## Buzzer Circuit Explanation

The buzzer is driven by a transistor circuit with all three pins of the 2N2222 NPN transistor connected:

1. **Base** - Connected to GPIO10 through a 1kΩ resistor
2. **Collector** - Connected to the negative terminal of the buzzer
3. **Emitter** - Connected to ground (GND)

The positive terminal of the buzzer connects to VCC/3.3V.

When GPIO10 outputs HIGH:
- Current flows through the resistor to the transistor base
- The transistor turns on (like a switch)
- Current can flow from VCC through the buzzer, through the collector to the emitter, and to ground
- The buzzer sounds

This circuit allows the buzzer to draw more current than the GPIO pin could safely provide directly.

## Operating Modes

The configuration supports three operating modes:

### 1. Development Mode (Default)
- WiFi enabled for easy debugging and updates
- Web server and API enabled
- OTA updates enabled
- More frequent sensor readings
- Deep sleep disabled
- Detailed logging

### 2. Production Mode
- WiFi disabled to save power
- Web server, API, and OTA updates disabled
- Deep sleep enabled (1s active, 9s sleep)
- Minimal logging
- Less frequent sensor readings

### 3. Temporary Configuration Mode
- Activated by pressing the config button in production mode
- Enables WiFi, API, OTA updates, and web server for 10 minutes
- Disables deep sleep temporarily
- Returns to production mode after timeout
- Indicated by triple buzzer beep when activated

To switch between development and production modes, change the `dev_mode` substitution in the YAML file:
```yaml
substitutions:
  dev_mode: "true"  # Set to "false" for production/battery-saving mode
```

## Configuration Details

The `walker_reminder.yaml` file contains the complete ESPHome configuration with the following key features:

### Power Optimization (Production Mode)

```yaml
# WiFi disabled
wifi:
  disabled: true

# Deep sleep enabled
deep_sleep:
  run_duration: 1s
  sleep_duration: 9s
  enabled: true
```

### BLE Tracking

The system uses BLE to track the distance between the walker and the wristband:

```yaml
esp32_ble_tracker:
  scan_parameters:
    interval: 100ms
    window: 50ms
    active: false
```

### Distance Calculation

Distance is calculated using RSSI values with:
- Moving average filter (5 samples)
- Calibrated RSSI-to-distance conversion
- Hysteresis to prevent alert oscillation (3.0m trigger, 2.7m reset)

### Battery Monitoring

The Seeed Studio XIAO ESP32C3 has built-in battery management, which we utilize for monitoring the battery level directly through GPIO1 without needing an external voltage divider.

## Setup Instructions

1. Install ESPHome (if not already installed)
   ```
   pip install esphome
   ```

2. Create a `secrets.yaml` file in the same directory with:
   ```yaml
   wifi_ssid: "your_wifi_ssid"
   wifi_password: "your_wifi_password"
   ota_password: "your_ota_password"
   ```

3. Modify the `walker_reminder.yaml` file:
   - Replace the placeholder UUID with your wristband's iBeacon UUID
   - Calibrate the `txPower` value by measuring RSSI at exactly 1 meter

4. Flash the Seeed Studio XIAO ESP32C3 (in development mode):
   ```
   esphome run walker_reminder.yaml
   ```

5. Hardware Assembly:
   - Connect the buzzer circuit as shown in the wiring diagram
   - Connect the config button to GPIO9 and GND
   - Connect the Li-ion battery to the XIAO ESP32C3's BAT+ and BAT- terminals
   - Place the ESP32 in the 3D printed case

6. When ready for deployment, change to production mode and flash again.

## Configuration Button Usage

In production mode, pressing the configuration button will:
1. Enable WiFi, OTA updates, and the web server for 10 minutes
2. Disable deep sleep during this period
3. Triple-beep the buzzer to indicate activation
4. Single-beep the buzzer when returning to power-saving mode

This allows for making configuration changes or updating firmware without having to open the case and connect via USB.

## Calibration

For accurate distance measurement:
1. Place the wristband exactly 1 meter from the ESP32
2. Measure the RSSI value (visible in logs during development mode)
3. Update the `txPower` value in the code with this measurement

## Battery Life Considerations

- Estimated battery life in production mode: ~60 days with 3000mAh Li-ion battery
- To extend battery life further:
  - Increase sleep duration (15-30s instead of 10s)
  - Reduce BLE scan window
  - Optimize BLE connection management