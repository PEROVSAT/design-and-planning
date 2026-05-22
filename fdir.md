# Fault Detection, Isolation, and Recovery
Though Low Earth Orbit (LEO) is still fairly protected by earths magnetosphere, some radiation gets through and can impact OBC operation

Though it is for OBC information, [this paper](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=10597570) has some great FDIR info

## Possible Events
The various ways that radiation can impact the system

### Cumulative Events
#### Total Ionizing Dose (TID)
> TID accounts for the amount of radiation that impacts electronic components during mission lifetime
The build-up can make transistors stop working after sufficient accumulation
> most CubeSat missions operate in LEO and with a short lifetime. This considerably reduces the risk of TID failures for such missions. Short-duration missions in LEOs typically encounter relatively low TIDs, ranging from 1 to 10 krad(Si) per year, depending on shielding 

I think that with our shielding, ISS orbit, and low mission duration, we can safely ignore this.

#### Displacement Damage (DD)
> the gradual degradation of the electrical and optical properties of semiconductor devices due to structural damage in the crystal lattice

> On satellites, the most affected components are photovoltaic cells in solar panels, charge-coupled devices (CCDs) used in many payloads as particle detectors, and photodiodes used in optical communications and camera chips

> The performance of MOS devices in OBCs is relatively insensitive to the DD, as they are surface devices that are less affected by bulk defects. 

According to [this study](https://doi.org/10.3390/app15169208) on PV degradation in LEO, we shouldn't expect any more than 5% power loss on our power cells over a 6 month mission duration. Furthermore, since we don't have active sun pointing, each cell will be in sun for around half of the time.

In conclusion, we can likely safely ignore this.

### Single Event Effects (SEEs)
Caused by radiation interaction with MOS devices (transistors)

#### Single Event Transients (SETs)
The particle hits a signal path, and can do various things like throw off clock timing.

> These are mostly soft errors, often repairable with power cycling

Should be addressed by watchdogs.

#### Single Event Upsets (SEUs)
The particle hits a flip-flop or memory unit, can can change the value of a bit. Can sometimes affect multiple adjacent bits, known as a multiple-bit upset (MBU).

> SEUs are considered a common occurrence for static random access memory (SRAM) devices exposed to radiation, since such devices are based on flip-flop technology. In contrast, flash devices, which are based on electrically erasable programmable read-only memory (EEPROM) technology, are immune to SEUs

This is also a soft error, so it can be fixed with a reset. Since flash memory is immune to these, all critical code should be stored and booted from flash memory.

In flight, ECC can help protect against SRAM errors, and watchdogs should catch anything that makes it through.

#### Single Event Latch-ups (SELs)
Particle energy is so high that it introduces a high current to the device.

> This is a hard error. \[...\] If there is no overcurrent protection circuit, the device will burn out.

I asked NSL if their EPS has this protection circuit, and while they seemed confused by the question, it sounds like it does not. From what I can tell, these are rare errors, but something we need to keep in mind.

## Solutions
From the paper, it seems like we need to be addressing SETs, SEUs, and SELs. SETs are generally an issue because they become SEUs, so I refer to them largely the same.

Shielding is not enough:
> Shielding, which is always employed in satellites, must therefore be rigorously combined with other mitigation techniques

### Hardware Requirements
The software will do everything in its power to protect against SEEs, but it can't handle everything. These aspects are things required of the hardware in order to minimize the chance that our system could get fully bricked.

#### ECC Flash Memory - SEU Protection
While flash memory is immune to SEUs, its controllers are still transistors that aren't. In addition, flash memory can suffer from non radiation based degradation.

To counter this, the on-board flash memory needs to have some form of ECC.

#### Latching Current Limiters (LCLs) - SEL Protection
Hardware is the only way to protect from SELs. NSL was unclear whether or not they have overcurrent protection in the EPS. We need to obtain the EPS datasheet for more information.

If the NSL EPS does not support this, the only realistic solution would be adding a small additional daughterboard with an LCL component to protect from these.

#### SEE sd.md FOR INFORMATION ON FDIR FOR SD CARDS

#### Hardware Watchdogs
Something can go incredibly wrong with software. The NSL EPS has a Heartbeat watchdog, where we send a little heartbeat to the EPS periodically to avoid it shutting us down. This is listed as a requirement, but this has already been confirmed by NSL

### Hardware Nice-To-Haves
While not strictly impossible to deal with on the software side, there are some things that the hardware could have that would make our lives a lot easier.

#### ECC SRAM - SEU Protection
While watchdogs can ensure correct flow, if an SEU changes the `state` variable in our finite state machine, it can have incorrect behavior that isn't caught by any watchdog.

Having at least a section of SRAM with ECC would be useful in protecting critical variables. However, this isn't a requirement, as there are software techniques (like variable duplication) that could protect against it.

#### FeRAM or MRAM - SEU Protection
Ferroelectric RAM and Magnetoresistive RAM are resistant to SEUs by physical design. In the exact same way as ECC SRAM, these would be nice to protect critical variables.

### Software Techniques
#### I2C Bit-Banging
I2C is finicky, even outside of space. The slave device can mess up the clock timing and lock up the bus. To fix this, our I2C driver has to support bit banging, which is where it manually toggles the clock pin a bunch of times to force the sensor to release the data line.

#### Variable Duplication
To further protect against SEUs, we can use a triple redundancy model for every critical variable. Upon access, we use the value that at least two variables use. That way, if an SEU hit one variable and changed it, we see that one being different than it should be, and we can have the correct value and still reset it.

#### Safe Mode Booting
Since watchdogs reset the system, everything in the system should start in the safest mode possible, where they diagnose everything about their system to ensure correctness before moving onto nominal operation.

#### Memory Scrubbing
ECC fixes on READ ONLY. In order to avoid multiple SEUs causing a MBU, we need to allow ECC to make corrections periodically. This can be done as a low-priority task that just reads through all active memory space with ECC, allowing it to catch and correct errors before they can compound into an MBU.

## Watchdog Layers
### Layer 1 - EPS Heartbeat Watchdog
Should everything go wrong on the OBC, this exists as the final failsafe, where if we don't send a heartbeat in time, it will power cycle our OBC. Resetting it to a known and working state if we properly secured the flash program memory.

Action: Full Power Cycle

### Layer 2 - Microcontroller Internal Watchdog
Pretty much any microcontroller we would buy should have an internal hardware watchdog. Once enabled, this is essentially a timer that counts down, which we need to reset before it hits 0.

Action: Microcontroller reset

### Layer 3 - Zephyr Software Watchdogs
Zephyr has a `Task WDT`  System you can set up. It ties into to the microcontroller watchdog, and feeds that.

Individual threads register with this, and get a channel_id. They send a heartbeat to the WDT system with `task_wdt_feed(channel_id)`.

Action: Gives a function that we write a few milliseconds to attempt a restoration of that thread. If it fails to achieve that in time, it resets the microcontroller.
