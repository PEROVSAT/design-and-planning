# Payload Thread
## Boot conditions
`DEPLOY_COMPLETE`

## Wakes on
Event driven (on filtered data)
OR
Interval: 500+ Epochs

## Pseudocode
* switch (`OP_STATUS`):
  * case **SAFE**:
    1. Ensure Eyestar is powered down
  * case **LOW**:
    1. Transmit beacon
      - This should only ever get triggered by interval, since filter is off during **LOW**
    2. Forward RX to **COMMANDS**
  * case **NOMINAL**, **HIGH**:
    1. Transmit **DFA** data
    2. Forward RX to **COMMANDS**

## Depends on:
- LittleFS
- System Health Global Flags

