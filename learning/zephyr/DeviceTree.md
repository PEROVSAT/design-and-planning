# The Zephyr DeviceTree
WIKI: [The DeviceTree](https://docs.zephyrproject.org/latest/build/dts/index.html)

## Basic Information
The DeviceTree describes the available hardware, and simplifies application code by providing easy calls to access hardware by name instead of pins

It is split into *sources* and *bindings*

## DeviceTree Overlays
TODO
## [Syntax and Hierarchy](https://docs.zephyrproject.org/latest/build/dts/intro-syntax-structure.html#nodes)
- The source acts a lot like a UNIX file tree, defining nodes (like directories) with subnodes
- The @ symbol can be used to define the base 
```
/dts-v1/;

/ {
        soc {
                i2c@40003000 {
                        compatible = "nordic,nrf-twim";
                        reg = <0x40003000 0x1000>;

                        apds9960@39 {
                                compatible = "avago,apds9960";
                                reg = <0x39>;
                        };
                        ti_hdc@43 {
                                compatible = "ti,hdc", "ti,hdc1010";
                                reg = <0x43>;
                        };
                        mma8652fc@1d {
                                compatible = "nxp,fxos8700", "nxp,mma8652fc";
                                reg = <0x1d>;
                        };
                };
        };
};
```
## Bus Configurations
TODO
## Pin Control
TODO
## Aliases
TODO
## Bindings
