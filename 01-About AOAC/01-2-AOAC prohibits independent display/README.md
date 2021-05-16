# AOAC prohibits independent display

## Description

-For notebooks with `AOAC` technology, it is recommended to disable the independent display to extend the machine's standby time.
-The patch method in this article is only applicable to machines with `AOAC` technology.

## `SSDT` patch example

-Patch 1: ***SSDT-NDGP_OFF-AOAC***
  -Query the name and path of the unique display and confirm the existence of the `_ON` and `_OFF` methods
  -Refer to the example, modify its name and path to be consistent with the query result
-Patch 2: ***SSDT-NDGP_PS3-AOAC***
  -Query the name and path of the independent display and confirm the existence of the `_PS0`, `_PS3` and `_DSM` methods
  -Refer to the example, modify its name and path to be consistent with the query result
-**Attention**
  -When querying the unique name and path as well as `_ON`, `_OFF`, `_PS0`, `_PS3` and `_DSM`, all `ACPI` files should be searched. It may exist in the `DSDT` file, or it may Exist in other `SSDT` files of `ACPI`.
  -The above 2 examples are customized patches for Xiaoxin PRO13, choose one to use. The unique name and path are: `_SB.PCI0.RP13.PXSX`.

## Note

-If both ***SSDT-NDGP_OFF*** and ***SSDT-NDGP_PS3*** meet the usage requirements, use ***SSDT-NDGP_OFF*** first
-For the detailed method of shielding independent display, please refer to "SSDT shielding independent display method"
