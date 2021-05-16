# Xiaoxin PRO13 Special Patch

## Special rename

PNLF renamed XNLF

```text
Find: 504E4C46
Replace: 584E4C46
```

The DSDT of Xiaoxin PRO has variable `PNLF`, `PNLF` and brightness patch name may conflict, so use the above name change to avoid it.

## Special AOAC patch

-***SSDT-NameS3-disable*** ——See "S3 Sleep Prohibition" in "AOAC Method"
-***SSDT-NDGP_OFF-AOAC*** —— Refer to "AOAC Prohibit Independent Display" in "AOAC Method"
-***SSDT-DeepIdle*** ——See "Power Idle Management" in "AOAC Method"
-***SSDT-PCI0.LPCB-Wake-AOAC*** ——See "AOAC Wake Method" in "AOAC Method"

## Other patches (reference)

-***SSDT-PLUG-_SB.PR00***-see "X86 injection"
-***SSDT-EC*** ——See "Counterfeit Equipment-Counterfeit EC"
-***SSDT-PNLF-CFL*** ——See "PNLF Injection Method"
-***SSDT-PMCR***-see "Add missing parts"
-***SSDT-SBUS*** ——See "SBUS/SMBU Patch"
-***SSDT-OCBAT1-lenovoPRO13***-See "Battery Patch"
-***SSDT-I2CxConf*** ——See "I2C Dedicated Components"
-***SSDT-OCI2C-TPXX-lenovoPRO13*** ——See "I2C Special Parts"
-***SSDT-CB-01_XHC*** ——See "ACPI Customized USB Port"
-***SSDT-GPRW***-See "060D Patch"
-***SSDT-RTC_Y-AWAC_N*** ——See "Binary Rename and Preset Variables"
-***SSDT-RMCF-PS2Map-LenovoPRO13*** ——This chapter patch, please refer to "PS2 Keyboard Map"
-***SSDT-OCPublic-Merge*** —— Patches in this chapter, see **Attachment** description
-***SSDT-BATS-PRO13*** ——See "Battery Patch"

**Note**: The name change required by the above patch is in the comment of the corresponding patch file.

## Remarks

-Please read "AOAC Method"

## Attachment: Merging common patches

-In order to simplify the operation and reduce the number of patches, some public patches have been merged into: ***SSDT-OCPublic-Merge***.

#### Merged Patch

-***SSDT-EC-USBX***-**USBX** part from OC official patch example
-***SSDT-ALS0*** ——The original patch is located in "Counterfeit Device-Counterfeit Ambient Light Sensor"
-***SSDT-MCHC***-The original patch is located in "Add Missing Parts"

#### Precautions

-***SSDT-OCPublic-Merge*** applies to all machines.
-After using ***SSDT-OCPublic-Merge***, the patches listed above **<u>Merged Patches</u>** are no longer applicable.
