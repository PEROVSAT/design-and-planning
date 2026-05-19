# F' Framework Notes
Tracking the main ideas of it, and the pros and cons in application to PEROVSAT

## Info
F' was made by NASA's JPL to "minimize development cost, schedule, or effort"
### Links
- [February 2026 Course Slides](https://github.com/fprime-community/fprime-course-materials/tree/master/Flight_Software_Workshop-February_2026)
- 

## Design
- Broken down into *components* which manage each part of the system
- Components communicate via *ports*
- *Events* are the history of actions, *channels* are current telemetry info
- Components are FULLY INDEPENDENT
  - Define requirements independently
  - Implemented and compiled independently
  - Tested independently
- NOT written in C or C++, rather a DSL that does codegen


## PEROVSAT Notes
- Though their website shows MSP430 support, it would be really tight and difficult to run
- We would be much better off with this if we had something like an STM32
