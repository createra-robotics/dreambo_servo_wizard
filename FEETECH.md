# FEETECH Documentation

## SMS Servo

# Magnetic Encoder SMS Servo - Memory Table Reference

## Table of Contents

- [1 Servo Communication Protocol](#1-servo-communication-protocol)
- [2 Servo Memory Table Definition](#2-servo-memory-table-definition)
    - [2.1 Version Information](#21-version-information)
    - [2.2 EEPROM Configuration](#22-eeprom-configuration)
    - [2.3 SRAM Control](#23-sram-control)
    - [2.4 SRAM Feedback](#24-sram-feedback)
    - [2.5 Factory Parameters](#25-factory-parameters)
- [3 Special Byte Explanations](#3-special-byte-explanations)
    - [3.1 Servo Phase](#31-servo-phase)
    - [3.2 Servo Status](#32-servo-status)
    - [3.3 Unloading Conditions](#33-unloading-conditions)
    - [3.4 LED Alarm Conditions](#34-led-alarm-conditions)

---

## 1 Servo Communication Protocol

The servo uses the FT-SCS custom protocol. The factory default serial port configuration for SMS servos is a baud rate of 115200 with RS485 bus communication. The baud rate can be configured in the range of 38400–1Mbps, and the default communication address (station ID) is 1.

**FT-SCS Custom Protocol**

---

## 2 Servo Memory Table Definition

When a function address uses two bytes of data, the **low byte** comes first (lower address) and the **high byte** comes second (higher address).

### 2.1 Version Information

| Address (DEC) | Address (HEX) | Function | Bytes | Initial Value | Access | Range | Unit | Description |
|---|---|---|---|---|---|---|---|---|
| 0 | 0x00 | Firmware Major Version | 1 | – | Read-only | | | |
| 1 | 0x01 | Firmware Minor Version | 1 | – | Read-only | | | |
| 2 | 0x02 | END | 1 | 0 | Read-only | | | 0 indicates little-endian storage structure |
| 3 | 0x03 | Servo Major Version | 1 | – | Read-only | | | |
| 4 | 0x04 | Servo Minor Version | 1 | – | Read-only | | | |

### 2.2 EEPROM Configuration

| Address (DEC) | Address (HEX) | Function | Bytes | Initial Value | Access | Range | Unit | Description |
|---|---|---|---|---|---|---|---|---|
| 5 | 0x05 | Servo ID | 1 | 1 | R/W | 0 ~ 253 | – | Unique master ID identifier on the bus |
| 6 | 0x06 | Baud Rate | 1 | 0 | R/W | 0 ~ 7 | – | 0–7 represent: 1000000(0), 500000(1), 250000(2), 128000(3), 115200(4), 76800(5), 57600(6), 38400(7) |
| 7 | 0x07 | Response Return Delay | 1 | 0 | R/W | 0/50 ~ 253 | 2 µs | Maximum settable return delay 254×2 = 508 µs; 0 means minimum return delay; values <50 default to 50 (100 µs) |
| 8 | 0x08 | Response Status Level | 1 | 1 | R/W | 0 ~ 1 | – | 0: No response packet returned except for read and PING commands; 1: Response packet returned for all commands |
| 9 | 0x09 | Minimum Angle Limit | 2 | 0 | R/W | 0 ~ 4094 | 0.087° | This value is 0 for multi-turn absolute position control |
| 11 | 0x0B | Maximum Angle Limit | 2 | 4095 | R/W | 1 ~ 4095 | 0.087° | This value is 0 for multi-turn absolute position control |
| 13 | 0x0D | Maximum Temperature Limit | 1 | 70 | R/W | 0 ~ 100 | °C | |
| 14 | 0x0E | Maximum Input Voltage | 1 | – | R/W | 0 ~ 254 | 0.1 V | |
| 15 | 0x0F | Minimum Input Voltage | 1 | 40 | R/W | 0 ~ 254 | 0.1 V | |
| 16 | 0x10 | Maximum Torque | 2 | 1000 | R/W | 0 ~ 1000 | 0.1% | On power-up, this value is assigned to address 48 (Torque Limit) |
| 18 | 0x12 | Phase | 1 | – | R/W | 0 ~ 254 | – | Special function byte; do not modify without specific need |
| 19 | 0x13 | Unloading Conditions | 1 | – | R/W | 0 ~ 254 | – | Setting a bit to 1 enables the corresponding protection; setting it to 0 disables it |
| 20 | 0x14 | LED Alarm Conditions | 1 | – | R/W | 0 ~ 254 | – | Setting a bit to 1 enables the corresponding LED flash alarm; setting it to 0 disables it |
| 21 | 0x15 | Position Loop P (Proportional) Coefficient | 1 | – | R/W | 0 ~ 254 | – | Motor proportional control coefficient (1/4) |
| 22 | 0x16 | Position Loop D (Derivative) Coefficient | 1 | – | R/W | 0 ~ 254 | – | Motor derivative control coefficient (1/8) |
| 23 | 0x17 | Position Loop I (Integral) Coefficient | 1 | 0 | R/W | 0 ~ 254 | – | Motor integral control coefficient |
| 24 | 0x18 | Minimum Starting Force | 1 | – | R/W | 0 ~ 254 | 0.1% | Sets the minimum output starting torque of the servo |
| 25 | 0x19 | Integral Limit Value | 1 | 0 | R/W | 0 ~ 254 | – | Maximum integral value = Integral Limit × 4; 0 disables the integral limit; effective in position modes 0 and 4 |
| 26 | 0x1A | Forward Dead Zone | 1 | 1 | R/W | 0 ~ 16 | 0.087° | Minimum unit is one minimum resolution angle |
| 27 | 0x1B | Reverse Dead Zone | 1 | 1 | R/W | 0 ~ 16 | 0.087° | Minimum unit is one minimum resolution angle |
| 28 | 0x1C | Protection Current | 2 | 511 | R/W | 0 ~ 2047 | 6.5 mA | Maximum settable current is 500 × 6.5 mA = 3250 mA |
| 30 | 0x1E | Angle Resolution | 1 | 1 | R/W | 1 ~ 128 | – | Magnification factor for the sensor's minimum resolution angle |
| 31 | 0x1F | Position Offset | 2 | 0 | R/W | 0 ~ 8191 | 0.087° | 0–2047 represents 0 to 2047; 2048–4095 represents 0 to −2047; 4096–6143 represents 2048 to 4095; 6144–8191 represents −2048 to −4095 (offset range −4095 to 4095) |
| 33 | 0x21 | Operating Mode | 1 | 0 | R/W | 0 ~ 2 | – | 0: Position servo mode; 1: Constant motor speed mode; 2: PWM open-loop speed mode; 3: Step mode |
| 34 | 0x22 | Holding Torque | 1 | 20 | R/W | 0 ~ 254 | 1% | Output torque after entering overload protection. e.g., 20 means 20% of maximum torque |
| 35 | 0x23 | Protection Time | 1 | 200 | R/W | 0 ~ 254 | 10 ms | Duration that the current load output exceeds the overload torque. e.g., 200 means 2 seconds; max 2.5 seconds |
| 36 | 0x24 | Overload Torque | 1 | 80 | R/W | 0 ~ 254 | 1% | Maximum torque threshold that triggers the overload protection timer. e.g., 80 means 80% of maximum torque |
| 37 | 0x25 | Speed Closed-Loop P Coefficient | 1 | – | R/W | 0 ~ 254 | – | Speed loop proportional coefficient in constant motor speed mode (mode 1) |
| 38 | 0x26 | Overcurrent Protection Time | 1 | 200 | R/W | 0 ~ 254 | 10 ms | Maximum settable: 254 × 10 ms = 2540 ms |
| 39 | 0x27 | Speed Closed-Loop I Coefficient | 1 | – | R/W | 0 ~ 254 | – | Speed loop integral coefficient in constant motor speed mode (mode 1) |

### 2.3 SRAM Control

| Address (DEC) | Address (HEX) | Function | Bytes | Initial Value | Access | Range | Unit | Description |
|---|---|---|---|---|---|---|---|---|
| 40 | 0x28 | Torque Switch | 1 | 0 | R/W | 0 ~ 2 | – | Write 0: Disable torque output; Write 1: Enable torque output; Write 128: Calibrate any current position to 2048 |
| 41 | 0x29 | Acceleration | 1 | 0 | R/W | 0 ~ 254 | 8.7 °/s² | Servo acceleration/deceleration; 0 means maximum acceleration |
| 42 | 0x2A | Target Position | 2 | 0 | R/W | −32767 ~ 32767 | 0.087° | Absolute position control; max corresponds to maximum effective angle; BIT15 is the direction bit |
| 44 | 0x2C | PWM Open-Loop Speed | 2 | 1000 | R/W | 0 ~ 1000 | 0.1% | Effective in PWM open-loop speed mode; BIT10 is the direction bit |
| 46 | 0x2E | Running Speed | 2 | Factory default max speed | R/W | −32767 ~ 32767 | 0.732 RPM / 0.0146 RPM | Controls maximum motor running speed; BIT15 is the direction bit. Speed 0 defaults to maximum speed; setting phase can make 0 mean stop. Speed unit is set via Phase: either 0.732 RPM or 0.0146 RPM. When unit is 0.0146 RPM, accuracy is still 0.732 RPM. |
| 48 | 0x30 | Torque Limit | 2 | Maximum Torque (addr 16); default 1000 | R/W | 0 ~ 1000 | 0.1% | User can modify this value programmatically to control the stall torque output |
| 50–54 | 0x32–0x36 | Undefined | 1 | | | | | |
| 55 | 0x37 | Lock Flag | 1 | 1 | R/W | 0 ~ 1 | – | Write 0: Disable write lock — values written to EEPROM addresses are saved across power cycles; Write 1: Enable write lock — values written to EEPROM addresses are NOT saved across power cycles |

### 2.4 SRAM Feedback

| Address (DEC) | Address (HEX) | Function | Bytes | Initial Value | Access | Range | Unit | Description |
|---|---|---|---|---|---|---|---|---|
| 56 | 0x38 | Current Position | 2 | – | Read-only | – | 0.087° | Returns the current absolute position; BIT15 is the direction bit. In step mode (mode 3), returns the step difference between current and target position; BIT15 is the direction bit |
| 58 | 0x3A | Current Speed | 2 | – | Read-only | – | 0.732 RPM / 0.0146 RPM | Returns the current motor rotation speed; unit depends on phase setting; BIT15 is the direction bit |
| 60 | 0x3C | Current Load | 2 | – | Read-only | – | 0.1% | Current voltage duty cycle driving the motor; BIT10 is the direction bit |
| 62 | 0x3E | Current Voltage | 1 | – | Read-only | – | 0.1 V | Current servo working voltage |
| 63 | 0x3F | Current Temperature | 1 | – | Read-only | – | °C | Current servo internal working temperature |
| 64 | 0x40 | Async Write Flag | 1 | 0 | Read-only | – | – | Flag bit when using the async write command |
| 65 | 0x41 | Servo Status | 1 | 0 | Read-only | – | – | A bit set to 1 indicates the corresponding error has occurred |
| 66 | 0x42 | Movement Flag | 1 | 0 | Read-only | – | – | 1 when the servo is moving; 0 when the servo has reached the target and stopped; remains 0 if no target position update is given |
| 67 | 0x43 | Target Position | 2 | 0 | Read-only | – | 0.087° | Current target position |
| 69 | 0x45 | Current Current | 2 | – | Read-only | – | 6.5 mA | Returns the current motor phase current |
| 71 | 0x47 | Undefined | 2 | – | Read-only | – | – | |

### 2.5 Factory Parameters

| Address (DEC) | Address (HEX) | Function | Bytes | Initial Value | Access | Range | Unit | Description |
|---|---|---|---|---|---|---|---|---|
| 80 | 0x50 | Movement Speed Threshold | 1 | – | Read-only | – | – | – |
| 81 | 0x51 | DTs (ms) | 1 | – | Read-only | – | – | – |
| 82 | 0x52 | Speed Unit Coefficient | 1 | – | Read-only | – | – | – |
| 83 | 0x53 | Hts (ns) | 1 | – | Read-only | – | – | 20.83 ns; effective for SMS servo firmware ≥ 2.54; 0 in other versions |
| 84 | 0x54 | Maximum Speed Limit | 1 | – | Read-only | – | – | Unit: 0.732 RPM |
| 85 | 0x55 | Acceleration Limit | 1 | – | Read-only | – | – | – |
| 86 | 0x56 | Acceleration Multiplier | 1 | – | Read-only | – | – | The acceleration multiplier takes effect when acceleration is 0; when both acceleration and acceleration multiplier are 0, the servo has the fastest response |

---

## 3 Special Byte Explanations

### 3.1 Servo Phase

| Bit (Weight) | Description |
|---|---|
| BIT0 (1) | Drive direction phase: (0) Forward, (1) Reverse |
| BIT1 (2) | Drive bridge mode: (0) Brushless, (1) Brushed (takes effect after restart) |
| BIT2 (4) | Speed unit: (0) 0.732 RPM, (1) 0.0146 RPM |
| BIT3 (8) | Speed mode: (0) Speed 0 = stop, (1) Speed 0 = maximum speed |
| BIT4 (16) | Angle feedback mode: (0) Single-turn angle feedback, (1) Full angle feedback |
| BIT5 (32) | Voltage sampling: (0) 1.5K low-voltage sampling, (1) 1K high-voltage sampling |
| BIT6 (64) | PWM frequency: (0) 24 kHz, (1) 16 kHz |
| BIT7 (128) | Position feedback direction phase: (0) Forward, (1) Reverse |

If multiple bits are set simultaneously, the servo phase value is the sum of the individual bit values. **Example:** Original phase value is 0; if the servo runs in reverse, the phase value is 128 + 1 = 129.

### 3.2 Servo Status

Servo status: 0 = normal, 1 = abnormal.

| Bit (Weight) | Description |
|---|---|
| BIT0 (1) | Voltage status |
| BIT1 (2) | Magnetic encoder status |
| BIT2 (4) | Temperature status |
| BIT3 (8) | Current status |
| BIT4 (16) | – |
| BIT5 (32) | Load status |
| BIT6 (64) | – |
| BIT7 (128) | – |

If multiple statuses occur simultaneously, the servo status value is the sum of the individual bit values. **Example:** Voltage over/under-voltage and servo overheat both occur, so the status value is 4 + 1 = 5.

### 3.3 Unloading Conditions

Unloading condition: 0 = disabled, 1 = enabled.

| Bit (Weight) | Description |
|---|---|
| BIT0 (1) | Voltage protection |
| BIT1 (2) | Magnetic encoder protection |
| BIT2 (4) | Overheat protection |
| BIT3 (8) | Overcurrent protection |
| BIT4 (16) | – |
| BIT5 (32) | Load overload |
| BIT6 (64) | – |
| BIT7 (128) | – |

If multiple bits are set simultaneously, the unloading condition value is the sum of the individual bit values. **Example:** Voltage protection and overheat protection are both enabled, so the unloading condition value is 4 + 1 = 5.

### 3.4 LED Alarm Conditions

LED alarm condition: 0 = disabled, 1 = enabled.

| Bit (Weight) | Description |
|---|---|
| BIT0 (1) | Voltage alarm |
| BIT1 (2) | Magnetic encoder alarm |
| BIT2 (4) | Overheat alarm |
| BIT3 (8) | Overcurrent alarm |
| BIT4 (16) | – |
| BIT5 (32) | Load overload alarm |
| BIT6 (64) | – |
| BIT7 (128) | – |

If multiple bits are set simultaneously, the LED alarm condition value is the sum of the individual bit values. **Example:** Voltage alarm and overheat alarm are both enabled, so the alarm condition value is 4 + 1 = 5.