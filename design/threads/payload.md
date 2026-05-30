# Payload Thread
## Boot conditions
Always

## Wakes on
Interval: 10 - 60 Epochs

## Pseudocode
* switch (`OP_STATUS`)
  * case **SAFE**:
    1. Depower all devices
  * case **LOW**:
    1. Depower AMUs
    2. Read: IMU, Sun Sensors, Magnetometer
    3. Store Results to LittleFS
  * case **NOMINAL**, **HIGH**:
    1. Power all payload
    2. Read: IMU, Sun Sensors, Magnetometer
    3. If Sun angle > threshold: Sweep all AMUs
    4. Store results to LittleFS

## Depends on:
- Sensor API
- LittleFS
- System Health Global Flags
