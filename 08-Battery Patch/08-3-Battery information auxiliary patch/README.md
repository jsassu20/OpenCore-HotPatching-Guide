# Battery information auxiliary patch

## Overview

-The newer ***VirtualSMC.kext*** and its components ***kext*** [after August 14] provide an interface for displaying battery auxiliary information. By customizing the SSDT patch, the battery's `PackLotCode`, `PCBLotCode`, `firmware version`, `hardware correction`, and `battery correction` can be displayed under the action of the driver.
-This patch comes from the official patch of ***VirtualSMC.kext*** with some adjustments
-This patch and the `battery` patch have no master-slave relationship. Non-essential!
-This patch applies to all laptops

### Patch Notes

-In the ACPI specification, `_BST` defines some battery information, and this patch uses the methods `CBIS` and `CBSS` to inject this information. For details on the definition of `_BST`, please refer to the ACPI specification

-In order to be able to use this patch on **machines that do not need a `battery` patch**, the B1B2 method has been added to the battery path of the sample patch

### SSDT-BATS-PRO13 example

-`Battery` path: `_SB.PCI0.LPCB.H_EC.BAT1`

  When using, ensure that the battery path of the original ACPI is consistent with the battery path of the example
  
-`CBIS` method
  
  -Find the corresponding variable according to the content of `_BST`, and press `low byte` and `high byte` to write B1B2. For example: PKG1 [0x02]=B1B2 (`FUSL`, `FUSH` ), if this variable is double-byte, please refer to the battery patch split data method to split the data and redefine the data
  -If you can't confirm the variable, you can view the battery-related information [**Unverified**] under win or Linux, and fill in its content directly. For example: `Firmware version` is 123, so PKG1 [0x04] = B1B2 (0x23, 0x01)

-`CBSS` method

  The data filling method is the same as `CBIS`
  
  **Note 1**: If you don’t need to fill in any content, delete everything from `PKG1 [Zero]=...` to `PKG1 [0x06]=...`, see example
  
  **Note 2**: `CBSS` method cannot be deleted
