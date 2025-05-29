# ESP32 Walker Reminder

A device to help disabled people remember to take their walker. The system consists of a wrist band with BLE and a battery-powered ESP32 attached to the walker. When the wrist band moves more than 3 meters away from the ESP32, an alert sound is triggered to remind the user to use their walker.

## Project Requirements

### Functional Requirements
- Remind users to take their walker when they move away from it
- Detect when the user is more than 3 meters away from the walker
- Provide a gentle audio reminder (not annoying but noticeable)
- Work reliably in home environments
- Easy to use with no user interaction required

### Technical Requirements
- Long battery life (target: several months between charges)
- Easy charging via USB-C
- Compact and unobtrusive design
- Secure attachment to walker
- Comfortable wristband for user
- Weather-resistant for occasional outdoor use
- Remote update capability without physical access to USB port

### Design Considerations
- **Power Efficiency**: The device must operate for extended periods on battery power
- **User Experience**: Alerts should be helpful reminders, not startling or annoying
- **Simplicity**: The system should work without requiring user configuration
- **Reliability**: Distance detection must be consistent and accurate
- **Maintainability**: Easy to update firmware and replace battery when needed

## System Components

- **Seeed Studio XIAO ESP32C3**: Compact microcontroller with built-in battery management and USB-C charging
- **Passive Buzzer**: For audio alerts when user moves away from walker
- **3000mAh Li-ion Battery**: Provides extended operation time
- **BLE Wristband**: iBeacon standard device worn by the user
- **Configuration Button**: For temporarily enabling WiFi and OTA updates
- **3D Printed Case**: Custom housing for electronics with walker mounting points

## Implementation Approach

The implementation follows a three-phase approach:

### Phase 1: Development
- Full debugging capabilities enabled
- WiFi connectivity for easy updates and monitoring
- Detailed logging for calibration and testing
- Frequent sensor readings for accurate tuning

### Phase 2: Production
- Power-saving features activated
- WiFi and unnecessary services disabled
- Deep sleep mode enabled
- Optimized sensor reading frequency

### Phase 3: Temporary Configuration Mode
- Activated by pressing a physical button
- Enables WiFi and OTA updates for 10 minutes
- Returns to production mode automatically after timeout
- Allows for remote updates without opening the case

## Directory Structure
- `/esphome/` - ESPHome configuration files
- `/case/` - 3D printable case design files

## Technical Details

### Power Management
- ESP32-C3 chosen for low power consumption
- WiFi disabled in production mode
- Deep sleep cycle: 1s active, 9s sleep
- Estimated battery life: ~60 days with 3000mAh Li-ion battery
- Built-in battery management on the XIAO ESP32C3 for charging and monitoring

### Distance Detection
- BLE RSSI-based distance estimation
- Moving average filter for stable readings
- Hysteresis to prevent alert oscillation (3.0m trigger, 2.7m reset)

### Installation
1. Flash the Seeed Studio XIAO ESP32C3 with the ESPHome configuration
2. Print the case and assemble the components
3. Mount the device on the walker
4. Configure the wristband as an iBeacon
5. Test and calibrate the system

For detailed implementation instructions, see the README files in the respective directories.