# Non-Volatile Storage in the OBC
The current model of data collection we are considering is frequent payload readings with intermediary storage, with later filtering before sending

This document is a stub, and needs to be flushed out.

The current design being considered for payload storage is NOR memory that we communicate with over SPI. It is very radiation tolerant, and having it over SPI means it has no transistors separate of the MCU that we would need to protect against SEEs
