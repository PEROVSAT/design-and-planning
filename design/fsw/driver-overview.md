# PEROVSAT Driver Overview
The PEROVSAT Flight software will use a combination of pre-built Zephyr communication protocol drivers and our own drivers on top of those for specific device interactions

## Zephyr Drivers
- UART
- I2C
- QSPI or OSPI

## Custom Drivers
### EPS Driver
Makes use of the Zephyr UART driver to abstract away EPS commands into C functions
### Eyestar Driver
Sits on top of the Zephyr UART driver to abstract Iridium communications to simple send and receive C functions. Also communicates with the EPS driver to control Eyestar power
### AMU Driver
Sits on Zephyr's I2C driver. Provides a logical grouping of AMUs into a single multi-channel AMU, regardless of if they are multi-channel in hardware.
