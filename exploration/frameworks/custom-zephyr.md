# Custom Zephyr

## Design
- kConfig allows very specific configuration of what aspects of the OS are needed
- The DeviceTree abstracts away hardware details to interfaces
  - Allows high level code to be hardware-agnostic, so the lab test code for the cells could be the same as on the sat
  - Many public driver written for common parts, but it can be difficult to write them if you need to make your own
- Has a more advanced IPC method than FreeRTOS through the `zbus`
- Fully decoupled tasks

## Goals
### Power Budget
Gemini suggests a minimum of 32KB of SRAM for the OS, but it can be pruned using the kConfig. It may be a little bit more computation than FreeRTOS, but with pruning I don't think its a ton more.

Notably, unlike FreeRTOS, Zephyr does REQUIRE a 32-bit device, so an MSP430 would be out of the question.

### Testability
Zephyr is likely the best option of any framework for testability.

The `ztest` tool provides really good unit testing
The `Twister` testing tool finds edge cases and analyzes performance
A special `native_sim` build makes SITL testing much easier. The DeviceTree makes it so you can mock a driver without having to pass fake data to drivers like in FreeRTOS

### Modularity
`zbus` IPC allows a publish subscribe model, which allows great separation of tasks. I don't think it's quite as good as F', but it is better than FreeRTOS.

### Learning Curve
It is initially fairly steep, mainly in the DeviceTree and kConfig stuff. However, once used to it, it could massively accelerate development with the abstractions provided.

## Conclusion
I would like to advocate for this architecture. The only thing that would really prevent us is if the microcontroller it would require would take too much power. We need to know more about power consumption before we could decide between this and FreeRTOS
