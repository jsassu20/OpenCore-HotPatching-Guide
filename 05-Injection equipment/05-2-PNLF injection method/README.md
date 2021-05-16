# OC-PNLF injection method

## Brightness (`PNLF`) The composition of the control part

-Drive:

  -WhateverGreen.kext built-in brightness driver (requires Lilu.kext)

    By default, WhateverGreen.kext will load the brightness driver. If you use other brightness drivers, you should disable its built-in brightness driver.

    -Disable method:

      -Add boot parameter `applbkl=0`
      -Modify the driver's `Info.plist\IOKitPersonalities\AppleIntelPanelA\IOProbeScore=5500`.

    -Download link: <https://github.com/acidanthera/WhateverGreen/releases>

  -IntelBacklight.kext
  
    -Download link: <https://bitbucket.org/RehabMan/os-x-intel-backlight/src/master/>
  
  -ACPIBacklight.kext
  
    -Download link: <https://bitbucket.org/RehabMan/os-x-acpi-backlight/src/master/>
  
-Patch

  -Custom brightness patch

    -***SSDT-PNLF-SNB_IVY***: 2nd and 3rd generation brightness patch.
    -***SSDT-PNLF-Haswell_Broadwell***: 4th and 5th generation brightness patch.
    -***SSDT-PNLF-SKL_KBL***: 6th and 7th generation brightness patch.
    -***SSDT-PNLF-CFL***: 8th generation + brightness patch.

      The above patch is inserted in `_SB`.

  -RehabMan brightness patch
  
    -[https://github.com/RehabMan/OS-X-Clover-Laptop-Config/blob/master/hotpatch/SSDT-PNLF.dsl](https://github.com/RehabMan/OS-X-Clover -Laptop-Config/blob/master/hotpatch/SSDT-PNLF.dsl)
  
    -[https://github.com/RehabMan/OS-X-Clover-Laptop-Config/blob/master/hotpatch/SSDT-PNLFCFL.dsl](https://github.com/RehabMan/OS-X-Clover -Laptop-Config/blob/master/hotpatch/SSDT-PNLFCFL.dsl)
  
    -[https://github.com/RehabMan/OS-X-Clover-Laptop-Config/blob/master/hotpatch/SSDT-RMCF.dsl](https://github.com/RehabMan/OS-X-Clover -Laptop-Config/blob/master/hotpatch/SSDT-RMCF.dsl)
  
      The RehabMan brightness patch is inserted in `_SB.PCI0.IGPU`. When using it, rename the `IGPU` of the patch file to the original name in ACPI (such as: `GFX0`).

## Common injection methods

-Drive: WhateverGreen
-Patch: Custom brightness patch or RehabMan brightness patch

## ACPI injection method

-Driver: ACPIBacklight.kext (need to disable WhateverGreen.kext built-in brightness driver, see the disable method above)
-Patch: see "ACPI Brightness Patch" method

## Other injection methods

Try it yourself according to the principle of driver + patch.

## Precautions

-When selecting a certain injection method, the drivers, patches, settings, etc. related to other methods should be cleared.

-When using custom brightness patches, please note that the patches are all `PNLF` devices injected under `_SB`. When there is a `PNLF` field in the original `ACPI`, it needs to be renamed, otherwise it will affect the `Windows` booting . You can also use [`RehabMan`'s patch](https://github.com/RehabMan/OS-X-Clover-Laptop-Config/tree/master/hotpatch). Renamed as follows:

  ```text
  // PNLF to XNLF
  Find: 504E 4C46
  Replace: 584E 4C46
  ```
