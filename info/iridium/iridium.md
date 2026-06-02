# Iridium Information

## Notes
In an ISS orbit, we will be in coverage of an iridium satellite 15% of the time [[David pg. 5](https://digitalcommons.usu.edu/cgi/viewcontent.cgi?article=4148&context=smallsat)]

An Iridium 9603 Modem (which the Eyestar S4 likely uses) supports a maximum of 340 bytes in a mobile originated message (data from the sat), and 270 bytes in a mobile terminated message (command to the sat) [[Iridium 9603 Developers Guide](https://telemetry.groupcls.com/wp-content/uploads/2018/12/Iridium-9603-9603N_Developers-Guide.pdf)]


## Command Flow
1. Enable Eyestar power with EPS command
2. Loop `AT+CSQ` (Check Signal Quality) to get in range of a sat
3. Use `AT+SBDWB` (Short Burst Data Write Binary) to load the packet
4. Use `AT+SBDIX` to initiate a session and wait. The `X` at the end makes it receive commands
5. Verify that the return code (`MO Status`) indicates a good send.
6. Check if a command is present in `MT Status`, and if there are more waiting from the `MT Queued`
7. If there are commands waiting, use `AT+SBDRB` (Short Data Burst Read Binary) to pull the command from the S4 into the OBC


