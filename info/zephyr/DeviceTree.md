# The Zephyr DeviceTree
WIKI: [The DeviceTree](https://docs.zephyrproject.org/latest/build/dts/index.html)

## Basic Information
The DeviceTree describes the available hardware, and simplifies application code by providing easy calls to access hardware by name instead of pins

## Hierarchy
### Layer 1: DeviceTree Source Include `.dtsi`
Found under:
- `/zephyr/dts/` for SoC definitions
- `/zephyr/boards` for parts of board definitions

These files can encapsulate any aspect of the hardware, and the `i` just shows that its intended to be included into a `.dts` that describes the board as a whole. You'll find these mainly defining the System-on-chips being used, as well as in separating out parts of a board to smaller files.

`/zephyr/dts/arm/st/u5/stmu5.dtsi`
```dts
/ {
	cpus {
		#address-cells = <1>;
		#size-cells = <0>;

		cpu0: cpu@0 {
			device_type = "cpu";
			compatible = "arm,cortex-m33";
			reg = <0>;
			#address-cells = <1>;
			#size-cells = <1>;
			cpu-power-states = <&stop0 &stop1 &stop2>;

			mpu: mpu@e000ed90 {
				compatible = "arm,armv8m-mpu";
				reg = <0xe000ed90 0x40>;
			};
		};

		power-states {
# Omitted for brevity
		};
	};

	sram0: memory@20000000 {
		compatible = "zephyr,memory-region", "mmio-sram";
		zephyr,memory-region = "SRAM0";
	};
```

If they are being used to define the SoC, this is where we find the majority of the physical memory address mappings. Nearly everything built on top of the base SoC `.dtsi` files will simply reference labels defined in the file, like `sram0` in this example.

When used in board definitions, these can just represent any aspect of the board, so long as it gets `#include`d in a full definition. For example, the NUCLEO-u575zi-q defines `nucleo_u575zi_q-common.dtsi`, which defines configured LEDs and sets initial statuses and mappings of pins to the SoC's pin control.

### Layer 2: DeviceTree Source `.dts`
Found under `/zephyr/boards`

These are the top level of printed hardware description. They use `#import` to bring in all the various subsections of the board, defined as `.dtsi` files, and tie it all together in a cohesive board presented to the user.

The usefulness of looking at these files varies between manufacturers. For example, the `nucleo_u575zi_q.dts` file simply creates some aliases and partitions the flash memory:
```dts
/dts-v1/;
#include "nucleo_u575zi_q-common.dtsi"

/ {
	model = "STMicroelectronics STM32U575ZI-NUCLEO-Q board";
	compatible = "st,stm32u575zi-nucleo-q";

	chosen {
		zephyr,console = &usart1;
		zephyr,shell-uart = &usart1;
		zephyr,sram = &sram0;
		zephyr,flash = &flash0;
		zephyr,canbus = &fdcan1;
		zephyr,code-partition = &slot0_partition;
	};

	aliases {
		led0 = &blue_led_1;
		rtc = &rtc;
		sw0 = &user_button;
		pwm-led0 = &pwm_led_1;
		pwm-led1 = &pwm_led_2;
		watchdog0 = &iwdg;
		volt-sensor0 = &vref1;
		volt-sensor1 = &vbat4;
		die-temp0 = &die_temp;
	};
};

&flash0 {
	partitions {
		#address-cells = <1>;
		#size-cells = <1>;
		ranges;

# Definitions of partitions omitted for brevity
};
```

However, it is very important to note that if we make a custom board, or buy a board that lacks support in the Zephyr source tree (meaning the manufacturer has not taken the trouble to write the `.dts` for us), this is a part of the DeviceTree setup we will need to do ourselves.

### Layer 3: Overlay files `.overlay`
These are application-level files, so we write them ourselves. They are much more dynamic, and they primarily map the pins available on a board to devices that we have hooked up to them, as well as any initial configuration we need to do to the hardware (like setting clock speeds).

Best practice is to place them in the specific board under `/zephyr/boards/`, so that it is trivial for us to switch which board we are running software on, simply by telling Zephyr which board to use.

They should be named `board_name.overlay`

Here is an example overlay, which would define a BNO055 model IMU on an I2C pin, and Sun Sensors in two of the UART ports

```dts
/* nucleo_u575zi_q.overlay */

/ {
    /* 1. Define aliases so the application C code can easily find these specific devices
          without knowing which physical buses they are attached to. */
    aliases {
        imu = &bno055_imu;
        sun-sensor-left = &sun_uart_left;
        sun-sensor-right = &sun_uart_right;
    };
};

/* 2. Configure the I2C bus and add the IMU */
&i2c1 {
    status = "okay";

    /* Modify the default clock frequency if the IMU requires it */
    clock-frequency = <I2C_BITRATE_FAST>;

    /* Create the IMU child node.
       Syntax: <label>: <node-name>@<address> */
    bno055_imu: imu@28 {
        /* The compatible string binds this node to a specific Zephyr device driver */
        compatible = "bosch,bno055";

        /* The I2C address of the device (must match the @28 in the node name) */
        reg = <0x28>;
    };
};

/* 3. Configure the first UART and add the first sun sensor */
&usart2 {
    status = "okay";

    /* Override the board's default baud rate for this specific sensor */
    current-speed = <9600>;

    sun_uart_left: sun-sensor-1 {
        /* Generic compatible string for the example */
        compatible = "custom,sun-sensor";
    };
};

/* 4. Configure the second UART and add the second sun sensor */
&lpuart1 {
    status = "okay";
    current-speed = <9600>;

    sun_uart_right: sun-sensor-2 {
        compatible = "custom,sun-sensor";
    };
};
```

## Syntax
### Includes and Defines
a `#include` can be used to bring in hardware definitions from a `.dtsi`, or standard C bindings.
```dts
#include <st/u5/stm32u575Xi.dtsi>
#include <zephyr/dt-bindings/gpio/gpio.h>
```
A `#define` is a macro declaration, which should be used for most hardcoded values:
```dts
#define MY_CUSTOM_BAUD 115200
```

### Nodes
- Everything must be built off the root node, which is called `/`.
- Node structure: `[label:] <node-name>[@<unit-address>] { ... };`
- The unit address (if included) must match the `reg = <0x...>` line within the node

### Properties
These are definitions set within a node

Booleans: just the property name `boolean-value;`
Strings: `status = "okay";`
String Arrays: `compatible = "st,stm32-i2c", "st,stm32-i2c-v2";`
Numbers (single): `clock-frequency = <100000>;`
Numbers (multiple): `reg = <0x10000200 0x20>;`

### Important Properties
- `status` can be set to `"okay"` to enable something or `"disable"` to disable it
- `compatible` names the device driver that should be associated with the device
- `reg` defines memory addresses. We'll largely only use this to define pins used

### Phandles
You can reference something previously defined by using an ampersand (`&`) in front of its label. This can be used in overlays to modify existing configurations

### Deletions
- `/delete-node/ &node_label` can be used to remove a node definition from files lower in the hierarchy
- `/delete-property/ property_name` can be used inside of a node to remove previously defined properties

### Aliases & Chosen
Zephyr specifically enables the use of aliases and a `chosen` block.

Aliases let you specify some alternate names without direct modification of the `.dtsi` files

The `chosen` block lets you specify the devices to use for Zephyr features
```dts
/ {
  aliases {
    led0 = &led_green;
    uart = &usart1;
  };

  chosen {
    zephyr,console = &usart1;
    zephyr,sram = &sram0;
  };
};
```
