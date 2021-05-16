# ACPI Brightness Patch

## Description

When the commonly used injection method does not work, try this method.

### Patch: ***SSDT-PNLF-ACPI***

The patch may need to be modified to suit you. Modification method:

-Extract native ACPI
-Search for `_BCL`, `_BCM`, `_BQC` in all ACPI files, and record the name of the device they belong to, such as `LCD`.
-Modify `DD1F` in the patch file to the previously recorded name (`DD1F` is replaced with `LCD`). Refer to ***《Modified Diagram》***.
-Modify the `IGPU` of the patch file to the name of the ACPI graphics card (for example, replace `IGPU` with `GFX0`).

### Drive

-ACPIBacklight.kext
