# On Board Computer Planning
The OBC has to be powerful enough to support the framework/OS and our code that runs on it. Additionally, we may need some hardware support for FDIR, as well as running at a low power footprint.

## References
[CubeSat OBC State of the Art](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=10597570)

## OBC Requirements
### Flash Memory
- This is the read only memory that houses the bootloader and our flight software
- On the safest end, we would like >=1MB of this. On the absolute tightest, 256KB.
- Assuming it is EEPROM or NOR based, the memory itself is immune to radiation. The controlling transistors can still experience SEUs, however.
- I'm going to put ECC Flash as a nice-to-have. It is not super high risk and there are other ways of mitigating it
### RAM
- Between 256KB and 64KB is the ideal range
- Zephyr supports execute in place, which draws from flash instead of RAM for the program, but we still need some for variables
- ECC would also be nice for RAM, but watchdogs should largely handle any issues here with a reboot.
### Non-Volatile Memory
- The best option here is to solder NOR memory to an SPI port on the board. Highly radiation tolerant.
- The devboard needs to have EITHER empty SOIC-8 or WSON-8 pads, or just have SPI headers that we can make a simple daughterboard for.
### Compute Power
- Our tasks do not need a ton of compute, but more can actually be better for power draw, since it completes its tasks quicker
- The ideal range would be between 40 and 100 MHz
- An FPU would be great, since it would open the option of doing IV math on-board. Not strictly needed by the mission, but it would allow us to make better use of limited data.
### Power Draw
- Less than 0.25w active power consumption is most ideal.

## OBCs
### Industrial Options
#### [Kongsberg NanoAvionics SatBus 3C2](https://nanoavionics.com/cubesat-components/satbus-3c2/)
~$5100
- Highly performant ARM-Cortex-M7
- 1MB SRAM
- 2MB ECC Flash
- MicroSD Card Slot, 256 MB NOR-Flash, 1 MB FRAM
- Expected active power consumption: 0.330w
- On-board magnetometer and three gyroscopes

#### [Spacemanic Deep Thought](https://spacemanic.com/products/deep-thought/)
$6100 FM, $4100 EM

#### [NANOobc-2](https://www.skylabs.si/products/nanoobc-2/)
quote needed
- PicoSkyFT SoC processor
- 4MB MRAM (EDAC, LCL protected)
- 2MB SRAM (EDAC, LCL protected)
- 2GB highspeed redundant NAND Flash
- Power draw:0.8-1w

#### [AAC Clyde Krypten-M3](https://www.aac-clyde.space/what-we-do/space-products-components/command-data-handling/kryten-m3#expandable-form-panel-)
quote needed
- ARM Cortex-M3 processor
- 16MB MRAM
- 20kRAD
- Power draw: 0.4w

#### [Spacemanic EDDIE](https://spacemanic.com/products/eddie/)
$4500
- onboards sensors: Gyro, accelerometer, magnetometer, temperature
- MSP430
- 6MB FRAM
- Power draw: 0.06-0.1w

#### [GomSpace NanoMind A3200](https://gomspace.com/product/nanomind-a3200/)
quote needed
- High-performance AVR32 MCU
- 512 KB build-in flash
- 128 MB NOR flash
- 32 kB FRAM
- 32 MB SDRAM
- 3-axis sensor, gyroscope
- Typical power draw: 0.17w (0.9w MAX)

#### [C3S OBC](https://c3s.hu/subsystems/#obc)
quote needed
- ARM Cortex-M7
- 0.46W
- Radiation hardened

#### <10k options: 2nd Space MASTER OBC, Space Inventor OBC

### Custom
We had a team chat about this. It is a little on the risky side, and with the amount of time to design, print, and verify it, we would have likely spent enough on man hours to have been able just to buy the industrial options. We agreed it would be best to stick to a development board or COTS OBC

### Development Boards
Some evidence exists that cheaper development or single-board computers (SBCs) work in space, and many teams have been known to fly them.

[Reddit: RPI0 as OBC](https://www.reddit.com/r/cubesat/comments/17riia3/raspberry_pi_zero_as_the_obc/)
[NASA: Raspberry Pi in Space](https://nepp.nasa.gov/docs/papers/2021-Guertin-Raspberry-Pi-Guideline-CL-21-5641.pdf)

#### [NUCLEO-U575ZI-Q](https://docs.zephyrproject.org/latest/boards/st/nucleo_u575zi_q/doc/index.html)
Looks to be easily the best option on the dev board side:
- 2MB Flash with ECC
- SRAM:
  - 786 KB with ECC off
  - 722 KB ECC off + 322 KB ECC on
- Single precision FPU
- ~10mW power draw (!!!)
- Native Zephyr support
- Fairly accurate clocks for a devboard

#### (NUCLEO-L552ZE-Q)(https://docs.zephyrproject.org/latest/boards/st/nucleo_l552ze_q/doc/nucleol552ze_q.html)

