# ACPIDebug description

## Description

By adding debugging code in the ***SSDT-xxxx*** patch, you can see the patch or ACPI working process on the console for debugging the patch.

## Claim

-Drive
  -Install ***ACPIDebug.kext*** to `OC\Kexts` and add the driver list.
-Patch
  -Add the main patch ***SSDT-RMDT*** to `OC\ACPI`, and add the patch list.
  -Add ***custom patch*** to `OC\ACPI`, and add the patch list.

## Debug

-Open **Console** and search for **`Keywords`** (**`Keywords`** from ***Custom Patches***)
-Observe the console output

## Example

-Purpose

  -Observe how the `_PTS` and `_WAK` of `ACPI` receive `Arg0` after the machine sleeps and wakes up.

-Drivers and patches

  -***ACPIDebug.kext***-see above

  -***SSDT-RMDT***-see above

  -***SSDT-PTSWAK*** ——The patch has built-in parameter transfer variables `\_SB.PCI9.TPTS`, `\_SB.PCI9.TWAK`, which is convenient for other patches to use. See "PTSWAK Comprehensive Extension Patch"

  -***SSDT-BKeyQxx-Debug*** ——This patch is just an example. Debugging code has been added to the patch, which can execute the debugging code after the key is responded. In actual use, you can specify the brightness shortcut keys or other keys.

    **Note**: The name change required by the above patch is in the comment of the corresponding patch file.

-Observe the console output

  -Open the console and search for `ABCD-`

  -Complete a sleep and wake up process

  -Press the key specified by ***SSDT-BKeyQxx-Debug*** and observe the console output. Under normal circumstances, the display results are as follows:

    ```log
    13:19:50.542733+0800 kernel ACPIDebug: {"ABCD-_PTS-Arg0=", 0x3,}
    13:19:55.541826+0800 kernel ACPIDebug: {"ABCD-_WAK-Arg0=", 0x3,}
    ```

    The above display result is the value of `Arg0` after the last sleep and wake-up.

## Remarks

-Debug code can be diversified, such as: `\RMDT.P1`, `\RMDT.P2`, `\RMDT.P3`, etc., see ***SSDT-RMDT.dsl*** for details
-The above drivers and main patches are from [@RehabMan](https://github.com/rehabman)
