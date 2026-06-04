# Zephyr Research
We are aiming to use Zephyr as the OS for PEROVSAT. It has a lot of really nice features for testing and clean design, but it has a bit of a learning curve as well.

This subdirectory of the repository aims to take some notes on all of the important conceptual aspects of Zephyr that a new dev to the team may need to understand before developing code for it.

## References
[Tutorial: Mastering Zephyr Driver Development](https://www.youtube.com/watch?v=o-f2qCd2AXo)
This is a fantastic source to get a start on pretty much all the basics of Zephyr. He covers the DeviceTree and it's usage, as well as how to implement a driver with both the sensor API and a custom API

## Summary
TODO

## Sections
### The DeviceTree
Goes over the three hierarchy levels to it and basic syntax

### Kconfig
Used as build configuration to tell CMake which pieces of code to include (subsystems, drivers, modules, etc.)

### Drivers
Drivers sit between hardware and application code to take care of the finer details. Zephyr has a very specific way of defining these and is quite macro heavy.

### Thread Model
TODO

### Inter-Process Communication (IPC)
TODO

### Watchdog System
TODO

### Power Management System
TODO

### Meta Tool Usage
TODO

### Testing
TODO
