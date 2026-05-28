# F' Framework Notes
Tracking the main ideas of it, and the pros and cons in application to PEROVSAT

## Info
F' was made by NASA's JPL to "minimize development cost, schedule, or effort"
### Links
- [February 2026 Course Slides](https://github.com/fprime-community/fprime-course-materials/tree/master/Flight_Software_Workshop-February_2026)
- [Documentation](https://fprime.jpl.nasa.gov/latest/docs/)

## Design
- Broken down into *components* which manage each part of the system
- Components communicate via *ports*
- *Events* are the history of actions, *channels* are current telemetry info
- Components are FULLY INDEPENDENT
  - Define requirements independently
  - Implemented and compiled independently
  - Tested independently
- NOT written in C or C++, rather a DSL that does codegen


## Goal Evaluation
### Power Budget Requirements
From what I can tell, we would need at least 32KB of RAM, and at least 128KB of static program memory. That puts us beyond the MSP430. In addition, it requires 32-bit.
### High testability
It has unit testing harnesses built-in, so that isn't difficult to add. In addition, the high decoupling between components makes it highly individually testable. It is difficult to find information on it, but it seems like SITL testing is possible, but not directly integrated into the framework
### High modularity
F' is probably the best of any option for this. The whole philosophy of the design is made to have separated components with very explicit ports in and out.
### Lower learning curve
Though it does try to abstract away a lot of difficult stuff, it is a custom DSL that any incoming students would have to learn. I think this could be classified as a moderate learning curve. Documentation looks pretty good.

## PEROVSAT Notes
- Though their website shows MSP430 support, it would be really tight and difficult to run
- We would be much better off with this if we had something like an STM32
