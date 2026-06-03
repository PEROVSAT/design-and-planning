# design-and-planning

## This Repository
This repository is made to store all the design and notes pertinent to the software team. It is broken up into three subsections.

## Exploration
Used at the beginning of the mission as we considered various design decisions

### Fault Detection, Isolation, and Recovery
How do we deal with radiation in space, as well as any other hardware issues that may occur?

### On-Board Computer
This is the most important piece of the mission hardware to our team. It defines the memory and compute limitations we are working with, as well as supplying some hardware reliability features for things we cannot handle in software

We explore various requirements of the board for it to meet our software goals, in addition to looking at some initial options for the hardware team to look at in more depth.

### Frameworks and Operating Systems
Frameworks and operating systems exist to provide various levels of abstraction from the bare metal. This section explores what options are available, and the tradeoffs each has for the features it provides.

We considered:
- NASA's core Flight System (cFS)
- JPL's F-Prime (F')
- Custom framework on top of Zephyr
- Custom framework on top of FreeRTOS
- Custom framework on bare metal (all code made in-house)

From that research, we have ended up going with Zephyr. It offers a lot of advanced features and a clean architecture, while not being a black box for what is actually running.

## Information
These are notes detailing information about things we'll be using in this project.

### Zephyr
In the exploration phase, we decided on Zephyr as our best option. While Zephyr provides a lot of great features, it adds a lot of additional complexity. We cover the following aspects of the OS:

Completed Notes:
- The DeviceTree

TODO:
- Kconfig
- Driver Model
- Thread Model
- Inter-Process Communication (IPC)
- Watchdog Subsystem
- Power Management Subsystem
- LittleFS
- Meta Tool Usage
- Unit Testing

### Basilisk
Basilisk is a very advanced simulation framework, but it is also a lot to learn. We should not spend much time learning this until the flight software is mostly complete.

### Iridium
Iridium is the global satellite network we'll be using to do communications. It has a lot of quirks and tradeoffs, which are noted in here alongside all the relevant documentation we've found.

## Design
Details the actual design we do for the project. Anybody new to the software team trying to get a high-level understanding of how the parts work should start here.
