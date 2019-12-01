# OC- PNLF injection method
*Brightness (`PNLF`) Control Section*

## Driver

**WhateverGreen.kext** has a built-in brightness driver (requires Lilu.kext)

- By default, WhateverGreen.kext loads the brightness driver. If you use another brightness driver, you should disable its built- in brightness driver.

### Disable method:

 - Add boot parameter 
 
        `applbkl = 0`
 
 - Modify the driver's 

        `Info.plist\IOKitPersonalities\AppleIntelPanelA\IOProbeScore=5500`
 

## Backlight Options

  - Whatevergreen.kext: [<https://github.com/acidanthera/WhateverGreen/releases>]()

- IntelBacklight.kext - [https://bitbucket.org/RehabMan/os- x- intel- backlight/src/master/]()

- ACPIBacklight.kext - [https://bitbucket.org/RehabMan/os- x- acpi- backlight/src/master]()
 
Custom brightness patches
-
`***SSDT- PNLF- SNB_IVY***: 2nd and 3rd generation brightness patches`
`***SSDT- PNLF- Haswell_Broadwell***: Brightness patches for 4, 5th generation`
`***SSDT- PNLF- SKL_KBL***: 6th and 7th generation brightness patches`
`***SSDT- PNLF- CFL***: 8th generation + brightness patch`.

-
#### ***The above patch is inserted in `_SB`***

  - RehabMan brightness patch
  
    - [https://github.com/RehabMan/OS- X- Clover- Laptop- Config/blob/master/hotpatch/SSDT- PNLF.dsl](https://github.com/RehabMan/OS- X- Clover - Laptop- Config / blob / master / hotpatch / SSDT- PNLF.dsl)
  
    - [https://github.com/RehabMan/OS- X- Clover- Laptop- Config/blob/master/hotpatch/SSDT- PNLFCFL.dsl](https://github.com/RehabMan/OS- X- Clover - Laptop- Config / blob / master / hotpatch / SSDT- PNLFCFL.dsl)
  
    - [https://github.com/RehabMan/OS- X- Clover- Laptop- Config/blob/master/hotpatch/SSDT- RMCF.dsl](https://github.com/RehabMan/OS- X- Clover - Laptop- Config / blob / master / hotpatch / SSDT- RMCF.dsl)
  
      The RehabMan brightness patch was inserted in `_SB.PCI0.IGPU`, and when used, the` IGPU` of the patch file was renamed to the original name in ACPI (eg `GFX0`).

## Common injection methods

- Drive: WhateverGreen
- Patch: Custom Brightness Patch or RehabMan Brightness Patch

## ACPI injection method

- Driver: ACPIBacklight.kext (the built- in brightness driver of WhateverGreen.kext needs to be disabled, see the disabling method above)
- Patch: See "ACPI Brightness Patch" method

## Other injection methods

Try it yourself by following the principles of drivers + patches.

## Precautions

When choosing an injection method, you should clear the drivers, patches, and settings related to other methods.
