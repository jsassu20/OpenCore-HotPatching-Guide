# Disable EHCx

## Description

EHC1 and EHC2 buses need to be disabled for one of the following situations:

- ACPI contains EHC1 or EHC2, and there is no related hardware in the machine itself.
- ACPI contains EHC1 or EHC2. The machine has related hardware but does not have actual output ports (external and internal).

## Patch

- ***SSDT-EHC1_OFF***: Disable `EHC1`.
- ***SSDT-EHC2_OFF***: Disable `EHC2`.
- ***SSDT-EHCx_OFF***: is a merged patch of ***SSDT-EHC1_OFF*** and ***SSDT-EHC2_OFF***.

## Instructions

- Priority BIOS setting: `XHCI Mode` =` Enabled`.
- If the BIOS does not have the `XHCI Mode` option, and one of the conditions described is met, the above patch is used.

### Precautions

- Applies to 7, 8, 9 series machines, and macOS version 10.11 or later.
- For 7 series machines, ***SSDT-EHC1_OFF*** and ***SSDT-EHC2_OFF*** cannot be used simultaneously.
