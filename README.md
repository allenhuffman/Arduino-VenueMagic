This document was created by GitHub A.I. Read with caution...

# Arduino-VenueMagic

Arduino-based digital and analog input monitoring system designed to interface with VenueMagic show control software, providing a cost-effective trigger interface for entertainment and show control applications.

## Overview

This project transforms an Arduino into a comprehensive input monitoring system that converts physical button presses, sensor triggers, and analog changes into serial commands for controlling lighting, audio, and other show elements. Originally designed as an affordable alternative to expensive dedicated show control input interfaces.

## Background

Here is the original series of articles that led to this code:

**http://subethasoftware.com/2013/02/13/how-i-got-started-with-arduino/**

## Project Components

### ArduinoDI - Digital Input Monitor
**File**: `ArduinoDI/ArduinoDI.ino`

A focused digital input monitoring system that:
- Monitors pins 2-12 for button presses and switch changes
- Provides debounced input detection (100ms default)
- Emits ASCII characters via serial communication
- Includes watchdog timer for system reliability

**Serial Output Format**:
- **Uppercase letters** (A-K) for "pin connected" (active low - button pressed)
- **Lowercase letters** (a-k) for "pin disconnected" (button released)

### ArduinoAID - Analog + Digital Input Monitor  
**File**: `ArduinoAID/ArduinoAIDI.ino`

Enhanced version supporting both digital AND analog inputs with advanced features:
- **Digital inputs**: Pins 2-12 (same as ArduinoDI)
- **Analog inputs**: Pins A0-A5 with auto-calibration
- **Smart calibration**: 5-second startup calibration period
- **Manual recalibration**: Via `!` serial command
- **Configurable notifications**: Toggle "off" events with `@` command

## Technical Specifications

### Hardware Configuration
```cpp
// Digital Input Pins
#define DI_PIN_START  2     // First digital input pin
#define DI_PIN_END    12    // Last digital input pin  
#define DI_PIN_COUNT  11    // Total digital pins (2-12)

// Analog Input Pins (ArduinoAID only)
#define AI_PIN_START  0     // A0
#define AI_PIN_END    5     // A5
#define AI_PIN_COUNT  6     // Total analog pins
```

### Pin Assignments
- **Pins 0-1**: Reserved for USB/Serial communication
- **Pins 2-12**: Digital inputs with internal pullup resistors
- **Pin 13**: Status LED (heartbeat/activity indicator)
- **Pins A0-A5**: Analog inputs (ArduinoAID version only)

### Debouncing System
- **Digital inputs**: 50-100ms debounce prevents false triggers
- **Analog inputs**: 10ms debounce for sensor stability  
- **Rollover-safe timing**: Handles Arduino millis() counter rollover correctly

### Auto-Calibration (ArduinoAID)
```cpp
#define ANALOG_CALIBRATION_MS 5000  // 5-second calibration on startup
```
- Automatically learns min/max values for each analog input during startup
- Adapts to different sensor types and environmental conditions
- Manual recalibration available via serial command

## Serial Communication Protocol

### Connection Settings
- **Baud Rate**: 9600
- **Data Format**: 8N1 (8 data bits, no parity, 1 stop bit)

### Command Interface
- **`?`** - Display current status of all pins
- **`!`** - Recalibrate analog inputs (ArduinoAID only)
- **`@`** - Toggle "off" event notifications (ArduinoAID only)

### Output Format
**Digital Pins** (both versions):
```
Pin 2 pressed  → "A"    Pin 2 released → "a"
Pin 3 pressed  → "B"    Pin 3 released → "b"
...
Pin 12 pressed → "K"    Pin 12 released → "k"
```

**Analog Pins** (ArduinoAID only):
```
A0 triggered → "L"    A0 released → "l"
A1 triggered → "M"    A1 released → "m"
...
A5 triggered → "Q"    A5 released → "q"
```

## VenueMagic Integration

### Show Control Interface
This system was specifically designed for **VenueMagic** show control software to provide:
- **Cost-effective alternative** to expensive dedicated input interfaces
- **ASCII character mapping** for easy trigger assignment in show control software
- **Real-time response** for precise show timing requirements
- **Multiple input types** supporting various sensors and switches

### Typical Use Cases
- **Theme park attractions** - Button/sensor triggered effects
- **Theater productions** - Actor-triggered lighting and sound cues  
- **Museum exhibits** - Visitor interaction triggers
- **Haunted attractions** - Motion sensor and button activated scares
- **Interactive installations** - Multi-sensor environmental responses

## Status Indication

### LED Feedback
- **Pin 13 LED**:
  - **Blinking**: Heartbeat when no inputs are active (system alive indicator)
  - **Solid ON**: When any input is currently triggered
  - **OFF**: When system is inactive (heartbeat overridden by active inputs)

### Serial Status Output
Example status display from `?` command:
```
ArduinoAIDI 0.3 by Allen C. Huffman (alsplace@pobox.com)
11 DI Pins (2-12), 50ms Debounce. 6 AI Pins (0-5), 10ms Debounce.
Digital Inputs: D2:1 D3:1 D4:1 D5:1 D6:1 D7:1 D8:1 D9:1 D10:1 D11:1 D12:1
Analog Inputs : A0:523 A1:512 A2:501 A3:498 A4:515 A5:520
Calibrated AIs: A0(495-545) A1(500-525) A2(490-515) A3(485-510) A4(505-530) A5(510-535)
```

## Development History

### Version Timeline
- **v0.0 (2012-10-09)** - Initial digital input version
- **v0.1 (2012-10-11)** - Fixed timing rollover bugs, improved debouncing
- **v0.2 (2012-10-15)** - Added analog input support
- **v0.3 (2012-10-16)** - Added manual recalibration command

### Implementation Notes
- **Author**: Allen C. Huffman (alsplace@pobox.com)
- **Testing**: Limited to VenueMagic 15-day trial (author didn't own full software)
- **Goal**: Create affordable show control input interface for maker community

## Code Quality Features

### Robust Programming Practices
- **Watchdog timer implementation** for system reliability
- **Timing rollover handling** prevents millis() overflow issues
- **Input validation** and bounds checking
- **Modular function design** for maintainability
- **Comprehensive commenting** and documentation

### Error Prevention
```cpp
// Compile-time sanity checks
#if (DI_PIN_END >= LED_PIN)
#error PIN CONFLICT: PIN END goes past LED pin.
#endif

#if (DI_PIN_START < 2)  
#error PIN CONFLICT: PIN START covers 0-TX and 1-RX pins.
#endif
```

## Hardware Setup

### Basic Wiring
1. **Digital Inputs**: Connect normally-open buttons between digital pins (2-12) and ground
2. **Analog Inputs**: Connect sensors to analog pins A0-A5 (ArduinoAID only)
3. **Power**: Standard USB power to Arduino
4. **Communication**: USB cable to computer running VenueMagic

### Input Types Supported
- **Digital**: Pushbuttons, toggle switches, relay contacts, TTL logic signals  
- **Analog**: Potentiometers, light sensors, pressure sensors, distance sensors

## Building and Installation

### Requirements
- Arduino IDE
- Arduino Uno or compatible board
- USB cable for programming and communication

### Upload Process
1. Open desired sketch in Arduino IDE (`ArduinoDI.ino` or `ArduinoAIDI.ino`)
2. Select correct board and port
3. Upload to Arduino
4. Open Serial Monitor at 9600 baud to verify operation
5. Connect to VenueMagic or other show control software

## Legacy and Modern Applications

This project demonstrates excellent embedded programming practices and remains relevant for:
- **Educational purposes** - Learning Arduino input handling and serial communication
- **Show control applications** - Still viable for modern installations  
- **IoT projects** - Sensor monitoring and event triggering
- **Maker projects** - Multi-input monitoring systems

The code quality and documentation make it an excellent reference for similar input monitoring applications in entertainment, industrial, and educational contexts.

---

*Originally developed in 2012 as an affordable alternative to expensive show control hardware, this project showcases practical Arduino programming for real-world entertainment applications.*
