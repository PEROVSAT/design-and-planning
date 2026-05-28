# design-and-planning

## This Repository
This repository is made to store all the design and learning notes pertinent to the software team. It is broken up into three subsections
## Exploration
Used at the very beginning of the mission, this section mainly has initial research information on important aspects of the mission, including:
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

## Learning
This section is essentially stripped-down wikis for various technologies that a new member to the team will need to learn to get a full grasp of the project.

### Zephyr
In the exploration phase, we decided on Zephyr as our best option. While Zephyr provides a lot of great features, it adds a lot of additional complexity. We cover the following aspects of the OS:
- The DeviceTree
- Kconfig
- Driver Model
- Thread Model
- Inter-Process Communication (IPC)
- Watchdog System
- Power Management System
- Meta Tool Usage
- Testing

