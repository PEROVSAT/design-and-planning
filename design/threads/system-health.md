# System Health Thread
## Boot conditions
Always

## Wakes on
Interval: 1 to 5 epochs

## Pseudocode
1. Send EPS Heartbeat
2. Send Zephyr Heartbeat (or register)
3. Read RTC to Set `DEPLOY_COMPLETE`
4. Read EPS Power to Set `OP_STATUS` to **SAFE**, **LOW**, **NOMINAL**, **HIGH**
5. Start or ensure Started threads:
   * **PAYLOAD**: Always
   * **DFA**: `DEPLOY_COMPLETE` && `OP_STATUS` == **NOMINAL** or **HIGH**
   * **COMMUNICATIONS**: `DEPLOY_COMPLETE`
   * **COMMANDS**: `DEPLOY_COMPLETE`
6. switch (`OP_STATUS`):
   * case **SAFE**, **LOW**: Set low Clock Speed
   * default: Set normal Clock speed

## Depends on:
- EPS Driver
