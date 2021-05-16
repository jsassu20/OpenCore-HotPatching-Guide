# SSDT Shielding independent display method

## Two ways to block independent display

-`config` method

  -`DeviceProperties\Add\PciRoot(0x0)/Pci(0x2,0x0)` add

    ```text
    disable-external-gpu 01000000
    ```

  -Add boot parameters

    ```text
    boot-args -wegnoegpu
    ```

-**This method** —— SSDT shielding independent display method

## SSDT shielding independent display process

-Disable independent display during the initialization phase.
-Enable independent display during machine sleep to prevent the system from crashing when the independent display enters `S3` when it is disabled.
-Disable the independent display again after the machine wakes up.

## Patch Combination

-Comprehensive patch-***SSDT-PTSWAK***
-Shield independent display patch—— ***SSDT-NDGP_OFF*** [or ***SSDT-NDGP_PS3***]

## Example

-***SSDT-PTSWAK***

  Slightly, see "PTSWAK Comprehensive Extension Patch" for details.
  
-***SSDT-NDGP_OFF***

  -Query the name and path of the unique display and confirm the existence of the `_ON` and `_OFF` methods
  -Refer to the example, modify its name and path to be consistent with the query result
  
-***SSDT-NDGP_PS3***

  -Query the name and path of the independent display and confirm the existence of the `_PS0`, `_PS3` and `_DSM` methods
  -Refer to the example, modify its name and path to be consistent with the query result
  
-**Attention**

  -When querying the unique name and path as well as `_ON`, `_OFF`, `_PS0`, `_PS3` and `_DSM`, all `ACPI` files should be searched. It may exist in the `DSDT` file, or it may Exist in other `SSDT` files of `ACPI`.
  -The unique name and path in the example is: `_SB.PCI0.RP13.PXSX`.

## Precautions

-According to the requirements of **patch combination**, ***SSDT-PTSWAK*** and ***SSDT-NDGP_OFF*** must be used at the same time [or ***SSDT-NDGP_PS3***]
-If both ***SSDT-NDGP_OFF*** and ***SSDT-NDGP_PS3*** meet the usage requirements, use ***SSDT-NDGP_OFF*** first

**Note**: The above main content comes from [@RehabMan](https://github.com/rehabman)
