# ACPI brightness patch

## Description

When the common injection method does not work, try this method.

### Patch: ***SSDT-PNLF-ACPI***

Patches may need to be modified to suit you. Modification method:

- Extract native ACPI
- Search all ACPI files for `_BCL`,` _BCM`, `_BQC`, and record the name of the device to which they belong (eg` LCD`).
- Modify the `DD1F` in the patch file to the previously recorded name (` DD1F` replaced with `LCD`). Refer to *** "Modify Icon" ***.
- Modify the `IGPU` of the patch file to the graphics card name of ACPI (eg` IGPU` replaced by `GFX0`).

### Drive

- ACPIBacklightInjector.kext
