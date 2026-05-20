# design-and-planning

## Requirements
- Sweep Perovskite devices
- Transmit IV data over Iridium

## Goals
Targets to keep in mind with the following aspects
- Power Budget Requirements
  - Some frameworks may be more compute heavy, thus requiring a bigger microcontroller and more power
- High testability
  - Unit testing is a must, SITL would be nice
- High modularity
  - Cohesive modules that are very decoupled from each other
- Lower learning curve
  - If our software base is too steep of a learning curve, we'll have a hard time getting new students to engage with it

## Key Decisions
### Framework and OS
- Bare metal superloop
  - No discrete tasks or context switching, done in specific ordering
- Standalone RTOS
  - Choice generally between FreeRTOS or Zephyr
- Framework
  - cFS or F'
  - Still has an underlying RTOS (likely FreeRTOS)

### Over-The-Air Binary Updates
- Had this on RhokSat
- If we find a bug in the software, this would allow us to push an update in deployment

### Fault Detection, Isolation, and Recovery Handling
- What way(s?) should software handle FDIR?
- This also encompasses how we do safe mode

### Data Storage
- Filesystem on an external drive (like an SD card)?
- Raw placement in memory?

### Telemetry
- Packet Format
- Compression?
