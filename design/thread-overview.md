# Flight Software Thread Model
An Epoch is a standard unit to control how fast threads run in comparison to one another. An epoch will most likely be 1 second.

## System Health
Roles:
- Send heartbeats
- Set global flags for deployment completion and operation status
- Start threads when moving into their flags

Wakes on:
- Intervals
- Tentatively EPS messages, if it sends them

Communicates with:
- EPS Driver
  - In: Battery information
  - Out: Heartbeat, power control
- All other tasks, through global flags

## Payload
Roles:
- Take raw payload measurements using the sensor API
  - Quit readings with low sun exposure
- Place readings in LittleFS Storage

Wakes on:
- Variable Intervals (depending on power status)

Communicates with:
- Sensor API
- LittleFS
  - Out: Raw Payload data files
- Zephyr Power Management Subsystem
  - It is the only thing using the payload devices, so it should be responsible for their power status

## Data Filtering and Analysis (DFA)
Roles:
- Filter out the best of the raw payload data
- Delete old raw data post-filter
- (Tentatively) compute IV curves should power status allow it
- (Tentatively) compress the data for sending

Wakes on:
- Intervals (longer than payload)

Communicates with:
- LittleFS
  - In: Raw Payload Data
  - Out: Filtered Data payloads
- Communications thread
  - Out: Flag new filtered data available

## Communications
Roles:
- Handle Eyestar interaction on the application level
- Accumulate filtered data and send in chunks to Iridium
- Receive ground commands and forward them to the commands thread
- Send beacons and command ACKs when necessary

Wakes on:
- New filtered data available (flag from DFA thread)

Communicates with: Eyestar driver (which in turn talks to EPS driver, which in turn talks to Zephyr UART driver), Commands task if it gets commands after a session
- Eyestar driver
- Commands task
  - In: ACKs
  - Out: Commands received from driver

## Commands
Roles:
- Process ground commands
- Details/commands TBD

Wakes on:
- Communications thread command forward

Communicates with:
- Communications thread
  - In: Command data
  - Out: Acknowledgements on completion
- TBD, depends on commands
