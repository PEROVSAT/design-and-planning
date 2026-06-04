# Zephyr Driver Model

## DeviceTree Access
Zephyr drivers access their device in compile time through macros. Here are the ways to resolve a device:
`DT_NODELABEL(serial0)`: By node label
`DT_ALIAS(my_serial)`: By alias
`DT_CHOSEN(zephyr_console)`: By the set device in a `chosen` property
`DT_DRV_INST(inst)`: By an instance number (used for multiple of the same)

Additionally, you can fetch the parent node of a device (like the I2C bus given an I2C device) with `DT_BUS(node_id)`

## Device Structure
```c
struct device {
      const char *name;
      const void *config;
      const void *api;
      void * const data;
};
```

`name` is a unique identifier used to pull the device in Macros
`config` maps to a part of ROM that has details from the DeviceTree definition
`api` a function pointer table
`data` houses everything that the driver needs in SRAM, so any stateful things should be put in a struct and stored here

## Registration
To register a driver, a few things are required:

A definition of DT_DRV_COMPAT that matches the YAML binding
`#define DT_DRV_COMPAT custom_eyestar`

An initialization function

An optional power management function

And a macro block like the following:
```c
#define EYESTAR_DEVICE_INIT(inst)                                       \
    static struct eyestar_data eyestar_data_##inst;                     \
    static const struct eyestar_config eyestar_config_##inst = {        \
        /* Dynamically resolve parent UART controller from DeviceTree */\
       .uart_dev = DEVICE_DT_GET(DT_BUS(DT_DRV_INST(inst))),           \
    };                                                                  \
    DEVICE_DT_INST_DEFINE(inst,                                         \
                          eyestar_init,                                 \
                          NULL, /* No PM callback defined */            \
                          &eyestar_data_##inst,                         \
                          &eyestar_config_##inst,                       \
                          POST_KERNEL,                                  \
                          CONFIG_MODEM_INIT_PRIORITY,                   \
                          &eyestar_api_funcs);

/* Iterate over all Devicetree instances with status="okay" */
DT_INST_FOREACH_STATUS_OKAY(EYESTAR_DEVICE_INIT)

```

The parameters for `DEVICE_DT_INST_DEFINE` are:
1. Instance number
2. Initialization function
3. The data struct
4. The config struct
5. When in the boot process it should get initialized
  - These are mainly `PRE_KERNEL_1`, `PRE_KERNEL_2`, `POST_KERNEL`
  - It is important that if this driver relies on another, that one is done in an earlier stage
6. An int, 0-999, which specifies when to get initialized within each boot block (above)
7. The API defined as function pointers in a struct

## Application Usage
An application can get the device using the `DEVICE_DT_GET` macro:
```c
#define MODEM_NODE DT_NODELABEL(eyestar_modem)
const struct device *const modem_dev = DEVICE_DT_GET(MODEM_NODE);

if (!device_is_ready(modem_dev)) {
    /* Handle error: The driver's init_fn returned an error code during boot */
    return -ENODEV;
}
```
