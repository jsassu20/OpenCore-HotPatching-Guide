# Disable EHCx

## Description

EHC1 and EHC2 buses need to be disabled in one of the following situations:

-ACPI includes EHC1 or EHC2, and the machine itself does not have related hardware.
-ACPI includes EHC1 or EHC2, the machine has related hardware but no actual output port (external and internal).

## Patch

-***SSDT-EHC1_OFF***: Disable `EHC1`.
-***SSDT-EHC2_OFF***: Disable `EHC2`.
-***SSDT-EHCx_OFF***: It is a merged patch of ***SSDT-EHC1_OFF*** and ***SSDT-EHC2_OFF***.

## Instructions

-Priority BIOS setting: `XHCI Mode` = `Enabled`.
-If the BIOS does not have the `XHCI Mode` option, and it meets one of the conditions in **Description**, use the above patch.

### Precautions

-Applicable to machines of 7, 8, 9 series, and macOS is 10.11 or above.
-For 7 series machines, ***SSDT-EHC1_OFF*** and ***SSDT-EHC2_OFF*** cannot be used at the same time.
-The patch adds the `_INI` method under `Scope (\)`. If it overlaps with the `_INI` of other patches, the content in `_INI` should be merged.
