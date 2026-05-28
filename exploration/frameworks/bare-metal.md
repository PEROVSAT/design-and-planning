# Bare Metal Superloop

## Design
- We start with nothing, just a `main()` with a `while(1)`
- Tasks are done in a deterministic, sequential ordering

## Goals
### Power Requirements
Very minimal. Zero overhead, and what we write is what is on the OBC. Depending on what we decide for software tasks being done on-board, this could easily be <4KB

### High Testability
Unit testing is still possible, there just isn't any baked-in support. SITL would likely be impossible, or exceedingly difficult.

### High Modularity
Superloops provide almost zero modularity. Each aspect can be affected by the ones that came before it, and everything operates in the same memory space

### Learning Curve
CS students that have taken COMP251 and COMP231 could get by. Nothing framework specific to learn, but you need to know the actual hardware details of the board, even with an HAL

## Conclusion
The Nebraska team likely failed due to a software bug that was hard to see because of the coupling in their superloop. In addition, they did not do much testing, which isn't as easy in bare-metal as in an OS or framework.

A superloop is just too dangerous for determinism and modularity as compared to what an RTOS offers
