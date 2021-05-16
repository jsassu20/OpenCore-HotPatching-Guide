# AOAC Overview

## AOAC Technology

-The new laptop introduces a new technology-`AOAC`, namely: *Always On/Always Connected*. `AOAC` was proposed by the `Intel` company, aiming to maintain the network connection and data transmission when the computer is in sleep or hibernation mode. Simply put, the introduction of `AOAC` makes the notebook, like our mobile phone, never shut down and always online.

### How to judge `AOAC` machine

-Open `FACP.aml` of `ACPI` with MaciASL, search for `Low Power S0 Idle`, if =1, it belongs to `AOAC` machine. Such as:

  ```asl
  Low Power S0 Idle (V5): 1
  ```

-For the content of `AOAC`, please Baidu `AOAC`, `Lenovo AOAC`, `AOAC network card`, etc.

## AOAC problem

### Sleep failure problem

-Due to the contradiction between `AOAC` and `S3`, the machine using `AOAC` technology does not have `S3` sleep function, such as `Lenovo PRO13`. Such a machine will **sleep failure** once it enters the `S3` sleep. **Sleep failure** The main manifestations are: unable to be awakened after sleep, showing a dead state, and can only be forced to shut down. **Sleep failure** The essence is that the machine has been stuck in the sleep process, and has never been able to sleep successfully.
-Refer to "ACPI Specification" for the contents of `S3` sleep.

### Standby time problem

-**Forbid `S3`sleep** can solve the **sleep failure** problem, but the machine will no longer sleep. The problem that comes with no sleep is that in battery-powered mode, the machine's standby time is greatly reduced. For example, in the case of "menu sleep", "auto sleep", and "lid sleep", the battery consumes a lot of power, about 5%-10% per hour.

## AOAC solution

-Disable `S3` sleep
-Turn off the power supply of the independent display
-Power idle management
-Choose a better quality SSD: SLC>MLC>TLC>QLC (not sure)
-Update SSD firmware if possible to improve power management performance
-Use NVMeFix.kext to enable APST of SSD
-Enable ASPM (BIOS advanced options enable ASPM, patch enable L1)

## AOAC sleep, wake up

-`AOAC` sleep

  The above scheme can make the machine sleep, this kind of sleep is called `AOAC` sleep. The essence of `AOAC` sleep is that the system and hardware enter the idle state, which is not the traditional sense of `S3` sleep.

-`AOAC` wake up

  It is more difficult for the machine to wake up after entering the `AOAC` sleep, and usually requires the power button to wake it up. Some machines may need the power button + `PNP0C0D` method to wake up the machine.

## AOAC Patch

-Prohibit `S3` sleep-see "Prohibit S3 sleep"
-Disable the independent display patch-see "AOAC prohibits independent display"
-Power Idle Management Patch-See "Power Idle Management"
-AOAC wake-up patch-see "AOAC wake-up method"
-Second wake up patch-see "060D patch"
-Enable device LI-see "Setting ASPM Working Mode" in "Prohibiting PCI Devices and Setting ASPM Working Mode", thanks @iStar丶Forever for providing the method
-Control Bluetooth WIFI-see "Sleep automatically turn off Bluetooth WIFI", thanks to @i5 ex900 0.66%/h Huaxing OC Dreamn for providing the method

## Precautions

-The `AOAC` solution is a temporary solution. With the widespread application of `AOAC` technology, there may be better solutions in the future.
-`AOAC` sleep and wake up are different from `S3` sleep and wake up, the following patches are not applicable
  -"PTSWAK Comprehensive Extension Patch"
  -"PNP0C0E Sleep Correction Method"
-The same reason as above, `AOAC` cannot display the working status correctly during sleep, such as the no breathing light flashes during sleep.
-Non-`AOAC` machines can also try this method.
