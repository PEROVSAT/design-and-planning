# PEROVSAT Hardware Overview
These will be fairly minimal details, just saying what each part does and what communication protocol it uses

## Bus Hardware
### NSL's EPS
ROLE: This is the Electrical Power System that is provided on the NSL bus. We do not have datasheets for specific interactions yet, but we can assume that it has some method of accessing basic battery info, power generation, and power consumption.
COMMUNICATION: via UART

### Eyestar S4 Modem
ROLE: Iridium data transfers. We have little information yet on device specifics.
COMMUNICATION: via UART

## Payload Hardware
### Aerospace Measurement Units (AMUs)
ROLE: These are provided by Aerospace Corp. They can adjust the resistance (to modify voltage) to take sweeps of Perovskite cells, as well as temperature sensors.
COMMUNICATION: I2C

### Sun Sensors
ROLE: Measures sun illumination. We will likely have one on the +Z and one on the -X.
COMMUNICATION: likely I2C or SPI

### Inertial Measurement Unit (IMU)
ROLE: Measure rotational acceleration to know tumble rate.
COMMUNICATION: I2C

### Magnetometer
ROLE: Basically an advanced compass, gives data on magnetic field strength and directions. Can be helpful in knowing satellite location
COMMUNICATION: I2C

## Soldered Hardware
### NOR Flash
ROLE: Provide non-volatile storage to store payload data
COMMUNICATION: Quad-SPI or Octa-SPI
