# ARM mbed OS

This is Tom's fork of the mbed OS, used to add support for custom targets. Mbed as a library does not specifically support parts; it supports board-level implementations. 

Here's the quick version of how to use this fork with the mbed-os. For more information on adding custom targets, [https://os.mbed.com/docs/v5.8/tools/adding-and-configuring-targets.html|see the mbed docs on this subject]

## Using This Fork

#### 1. Install the mbed CLI

It's [https://github.com/ARMmbed/mbed-cli|here].

You can either install with pip: 

```
$ pip install mbed-cli
```

Or if you don't have pip (don't go get pip for this, just do this if you don't have it already):

```
$ git clone https://github.com/ARMmbed/mbed-cli
$ python setup.py install
```

#### 2. Create a new project.
 
```
$ mbed new my-project
```

#### 3. Point the project at this fork instead of at the original mbed library.

This is done by editing my-project/mbed-os.lib

Original: `https://github.com/ARMmbed/mbed-os/#cba28cc0ac2477b14e0cf2a3b4ea670aa17fe879`

This fork: `https://github.com/ersatzavian/mbed-os/#latest-commit-sha`

### 4. Add custom target in this fork.

## Adding Custom Targets

#### 1. Copy the target you're starting from to make a new target: 

```
$cp -r mbed-os/targets/TARGET_STM/TARGET_STM32F2/TARGET_NUCLEO_F207ZG mbed-os/targets/TARGET_STM/TARGET_STM32F2/TARGET_GEN_F207VG
```

#### 2. Edit PinNames.h, PeripheralPins.c

For my example, they're in `mbed-os/targets/TARGET_STM/TARGET_STM32F2/TARGET_TPL_F207VG`. Note that if the pin alias you're removing is referred to elsewhere in this file, you'll need to remove all the references to get your project to compile. Other changes may be necessary to get some libraries to work with your new target. For example, many libraries expect at least one pin named "LED1".

#### 4. Make sure your target exists in the mbed RTX header

```
diff --git a/targets/TARGET_STM/mbed_rtx.h b/targets/TARGET_STM/mbed_rtx.h
index 14fdd3380..af8c3ccf6 100644
--- a/targets/TARGET_STM/mbed_rtx.h
+++ b/targets/TARGET_STM/mbed_rtx.h
@@ -137,6 +137,22 @@
 #define OS_CLOCK                120000000
 #endif
 
+#elif defined(TARGET_STM32F207VG)
+
+#ifndef INITIAL_SP
+#define INITIAL_SP              (0x20020000UL)
+#endif
+#ifndef OS_TASKCNT
+#define OS_TASKCNT              14
+#endif
+#ifndef OS_MAINSTKSIZE
+#define OS_MAINSTKSIZE          256
+#endif
+#ifndef OS_CLOCK
+#define OS_CLOCK                120000000
+#endif
+
+
```


#### 5. Add your new target to custom_targets.json at the root of your project. 

There's one here for reference; you can copy it from the root of mbed-os to the root of your project to use it. 


```
{
    "GEN_F207VG": {
        "inherits": ["FAMILY_STM32"],
        "core": "Cortex-M3",
        "extra_labels_add": ["STM32F2", "STM32F207VG"],
        "config": {
            "clock_source": {
                "help": "Mask value : USE_PLL_HSE_EXTC | USE_PLL_HSE_XTAL | USE_PLL_HSI",
                "value": "USE_PLL_HSE_EXTC|USE_PLL_HSE_XTAL|USE_PLL_HSI",
                "macro_name": "CLOCK_SOURCE"
            }
        },
        "device_has_add": ["ANALOGIN", "ANALOGOUT", "CAN", "I2C", "SPI", "SERIAL_ASYNCH", "SERIAL_FC", "FLASH"],
        "features": ["LWIP"],
        "device_name": "STM32F207VG"
    }
}
```

# ARM Mbed References

[![Build status release][mbed-travis-release-svg]][mbed-travis-release] 
[![Build status master][mbed-travis-master-svg]][mbed-travis-master] 
[![Tools coverage status][mbed-coveralls-tools-svg]][mbed-coveralls-tools] 
[![PR progress][mbed-waffle-svg]][mbed-waffle] 

[mbed-os-link]: https://www.mbed.com/en/platform/mbed-os/
[mbed-travis-master]: https://travis-ci.org/ARMmbed/mbed-os
[mbed-travis-master-svg]: https://travis-ci.org/ARMmbed/mbed-os.svg?branch=master
[mbed-travis-release]: https://travis-ci.org/ARMmbed/mbed-os/branches
[mbed-travis-release-svg]: https://travis-ci.org/ARMmbed/mbed-os.svg?branch=latest
[mbed-coveralls-tools]: https://coveralls.io/github/ARMmbed/mbed-os?branch=master
[mbed-coveralls-tools-svg]: https://coveralls.io/repos/github/ARMmbed/mbed-os/badge.svg?branch=master
[mbed-waffle]: https://waffle.io/ARMmbed/mbed-os
[mbed-waffle-svg]: https://badge.waffle.io/ARMmbed/mbed-os.svg?columns=all

Arm Mbed OS is an open source embedded operating system designed specifically for the "things" in the Internet of Things. It includes all the features you need to develop a connected product based on an Arm Cortex-M microcontroller, including security, connectivity, an RTOS and drivers for sensors and I/O devices.

Mbed OS provides a platform that includes:
* Security foundations.
* Cloud management services.
* Drivers for sensors, I/O devices and connectivity. 

## Release notes
The [release notes](https://os.mbed.com/releases) detail the current release. You can also find information about previous versions.

## Getting started for developers
 
We have a [developer website](https://os.mbed.com) for asking questions, engaging with others, finding information on boards and components, using an online IDE and compiler, reading the documentation and learning about what's new and what's coming next in Mbed OS.

## Getting started for contributors
 
We also have a [contributing and publishing guide](https://os.mbed.com/contributing/) that covers licensing, contributor agreements and style guidelines.
