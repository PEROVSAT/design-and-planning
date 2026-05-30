# Data Filtering & Analysis (DFA)
## Boot conditions
`DEPLOY_COMPLETE` && `OP_STATUS` == **NOMINAL** or **HIGH**

## Wakes on
Interval: 150 - 500 Epochs

## Pseudocode
* Send heartbeat to Zephyr (or register)
* If no new data, exit
* switch (`OP_STATUS`):
  * case **HIGH**:
    1. Calc Face exposure
    2. Advanced IV analysis Details TBD
    3. Place analysis in LittleFS
    4. Delete raw data from LittleFS
    5. Wake **COMMUNICATIONS**
  * case **NOMINAL**:
    1. Calculate face exposure
    2. keep top X% of Sweeps
    3. Place filtered in LittleFS
    4. Delete RAW from LittleFS
    5. Wake **COMMUNICATIONS**
  * case **SAFE**, **LOW**:
    * exit
* **If** New Data? == False:
    * exit

## Depends on:
- LittleFS
- System Health Global Flags
- **COMMUNICATIONS** Message Queue

