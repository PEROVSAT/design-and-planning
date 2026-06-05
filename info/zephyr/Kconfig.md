# Zephyr's Kconfig
Kconfig (Kernel Config) comes from Linux, and it is used to configure exactly which parts of the kernel you want to include in your build.

It can be used to enable or disable any of the following:
- Kernel Features
  - Support for multi-threading, floating point, memory protection, etc
- Subsystems
  - Entire code stacks like Bluetooth, TCP/IP, and USB
- Drivers
  - Both custom and built-in drivers like I2C
- Modules
  - These are essentially libraries. These could be anything from Filesystems to random number generation

It works with CMake to include every bit of code that it needs to in the compilation process

## menuconfig and guiconfig
Menuconfig is a CLI utility for setting Kconfig values, while guiconfig is a gui-window to do the same.

Its usage is a bit hard to describe in this document, so I recommend looking into it through just using it and the following tutorials:
- [Kconfig Tutorial - Shawn Hymel](https://www.youtube.com/watch?v=dXx1Wc15NZE)
- [Zephyr Documentation - Interactive Kconfig Interfaces](https://docs.zephyrproject.org/latest/build/kconfig/menuconfig.html)

## Manual Config
Alternatively, you can directly set values in the `prj.conf` file. To get the values you want to set, you can use the menuconfig or guiconfig searches to set it in `.config` (below), and then just copy the values that set into your `prj.conf`

Though you can enable and disable things by setting `y` or `n`, convention is either to have it set as `y` or commented out entirely, that way you can use `#IFDEF` to check if its enabled more easily.

Additionally, since the values available to set can vary depending on which board you're using, you can put these files in a `boards` subdirectory, and tell Zephyr to use those individually at the build process. Alternatively, you can put differently named conf files to build it in different ways with just changing which file you use. You can just place these all in the same directory as `prj.conf`, then name it something like `prj_release.conf` and pass `--DCONF_FILE=prj_release.conf` in the west build command.

## .config File
After build, Kconfig will make a `.config` file that stores all the settings.

## Defining Symbols
In custom modules, you may want to add additional configuration settings that can be accessed through Kconfig.

Here's an example:
```
config USB_CONSOLE
     bool "USB console support"
     select CONSOLE
```

There are a lot more details on this in the [Zephyr docs](https://docs.zephyrproject.org/latest/build/kconfig/index.html)

