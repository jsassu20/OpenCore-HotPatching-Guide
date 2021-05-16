# PNP0C0E Sleep correction method

## `PNP0C0E` and `PNP0C0D` sleep mode

-ACPI specification

  `PNP0C0E` — Sleep Button Device

  `PNP0C0D` — Lid Device

  For details of `PNP0C0E` and `PNP0C0D`, please refer to the ACPI specification.

-`PNP0C0E` sleep conditions

  -Execute `Notify(***.SLPB, 0x80)`. `SLPB` is the name of the `PNP0C0E` device.
  
-`PNP0C0D` sleep conditions

  -`_LID` returns `Zero`. `_LID` is the current status of the `PNP0C0D` device.
  -Execute `Notify(***.LID0, 0x80)`. `LID0` is the name of the `PNP0C0D` device.

## Problem Description

Some machines provide a sleep button (little moon button), such as Fn+F4 for some ThinkPad, Fn+Insert for Dell and so on. When this button is pressed, the system executes the `PNP0C0E` sleep. However, ACPI incorrectly passed the shutdown parameter instead of the sleep parameter to the system, which caused the system to crash. Even if you can sleep, you can wake up normally, and the system's working status is destroyed.

One of the following methods can fix this problem:

-Intercept the parameters passed by ACPI and correct it.
-Convert `PNP0C0E` sleep to `PNP0C0D` sleep.

## solution

### Associated 3 patches

-***SSDT-PTSWAK***: Define the variables `FNOK` and `MODE` to capture the changes of `FNOK`. See "PTSWAK Comprehensive Extension Patch".

  -`FNOK` indicates the state of the button
    -`FNOK` =1: Press the sleep button
    -`FNOK` =0: Press the sleep button again or the machine is woken up
  -`MODE` set sleep mode
    -`MODE` =1: `PNP0C0E` sleep
    -`MODE` =0: `PNP0C0D` sleep

  Note: Set `MODE` according to your needs, but you cannot change `FNOK`.

-***SSDT-LIDpatch***: Capture `FNOK` changes

  -If `FNOK` =1, the current status of the lid device returns to `Zero`
  -If `FNOK` =0, the current state of the cover device returns to the original value

  Note: `PNP0C0D` device name and path must be consistent with ACPI.

-***Sleep button patch***: After the button is pressed, set `FNOK` = `1`, and perform corresponding operations according to different sleep modes

  Note: `PNP0C0D` device name and path must be consistent with ACPI.

#### Description of two sleep modes

-`MODE` =1 mode: When the sleep button is pressed, ***sleep button patch*** makes `FNOK=1`. ***SSDT-PTSWAK*** caught `FNOK` as `1`, and enforced `Arg0=3` (otherwise `Arg0=5`). Restore `FNOK=0` after waking up. A complete `PNP0C0E` sleep and wake-up process is over.
-`MODE` =0 mode: When the sleep button is pressed, in addition to completing the above process, ***SSDT-LIDpatch*** also captures `FNOK=1` and makes `_LID` return to `Zero` and execute `PNP0C0D` sleep. Restore `FNOK=0` after waking up. A complete `PNP0C0D` sleep and wake-up process is over.

The following are the main contents of ***SSDT-LIDpatch***:

```Swift
Method (_LID, 0, NotSerialized)
{
    if(\_SB.PCI9.FNOK==1)
    {
        Return (0) /* Return to Zero, which satisfies one of the PNP0C0D sleep conditions */
    }
    Else
    {
        Return (\_SB.LID0.XLID()) /* Return to the original value */
    }
}
```

The following is the main content of ***sleep button patch***:

```Swift
If (\_SB.PCI9.MODE == 1) /* PNP0C0E sleep */
{
    \_SB.PCI9.FNOK =1 /* Press the sleep button */
    \_SB.PCI0.LPCB.EC.XQ13() /* Original sleep button position, the example is TP machine */
}
Else /* PNP0C0D sleep */
{
    If (\_SB.PCI9.FNOK!=1)
    {
            \_SB.PCI9.FNOK =1 /* Press the sleep button */
    }
    Else
    {
            \_SB.PCI9.FNOK =0 /* press the sleep button again */
    }
    Notify (\_SB.LID, 0x80) /* Execute PNP0C0D sleep */
}
```

### Example of rename and patch combination: (Dell Latitude 5480 and ThinkPad X1C5th)

-**Dell Latitude 5480**

  PTSWAK was renamed: `_PTS` to `ZPTS`, `_WAK` to `ZWAK`.

  Lid status renamed: `_LID` to `XLID`

  Renamed button: `BTNV` to `XTNV` (Dell-Fn+Insert)

  Patch combination:

  -***SSDT-PTSWAK***: Comprehensive patch. Set `MODE` according to your needs.
  -***SSDT-LIDpatch***: Lid status patch.
  -***SSDT-FnInsert_BTNV-dell***: Sleep button patch.

-**ThinkPad X1C5th**

  PTSWAK was renamed: `_PTS` to `ZPTS`, `_WAK` to `ZWAK`.

  Lid status renamed: `_LID` to `XLID`

  Key rename: `_Q13 to XQ13` (TP-Fn+F4)
  
  Patch combination:
  
  -***SSDT-PTSWAK***: Comprehensive patch. Set `MODE` according to your needs.
  -***SSDT-LIDpatch***: Lid status patch. Modify `LID0` to `LID` in the patch.
  -***SSDT-FnF4_Q13-X1C5th***: Sleep button patch.
  
  **Note 1**: The sleep button of X1C5th is Fn+4, and the sleep button of some TPs is Fn+F4.
  
  **Note 2**: TP machine `LPC` controller name may be `LPC` or `LPCB`.

### Other machines repair `PNP0C0E` sleep

-Use patch: ***SSDT-PTSWAK***; renamed: `_PTS` to `ZPTS`, `_WAK` to `ZWAK`. See "PTSWAK Comprehensive Extension Patch".

  Modify `MODE` according to your needs.

-Use patch: ***SSDT-LIDpatch***; renamed: `_LID` to `XLID`.

  Note: `PNP0C0D` device name and path must be consistent with ACPI.

-Find the position of the sleep button, make ***sleep button patch***

  -Normally, the sleep button is `_Qxx` under `EC`, this `_Qxx` contains `Notify(***.SLPB,0x80)` command. If it can't be found, DSDT will search the full text `Notify(***.SLPB,0x80)` to find its location, and then look up the original location step by step.
  -Make sleep button patch and rename if necessary with reference to the example.

  Note 1: SLPB is the name of the `PNP0C0E` device. If you confirm that there is no `PNP0C0E` device, add a patch: SSDT-SLPB (located in "Add Missing Parts").

  Note 2: `PNP0C0D` device name and path should be consistent with ACPI.

### `PNP0C0E` Sleep features

-The sleep process is slightly faster.
-The sleep process cannot be terminated.

### `PNP0C0D` Sleep features

-During sleep, press the sleep button again to terminate sleep immediately.

-When the external display is connected, after pressing the sleep button, the working screen becomes the external display (the internal screen is off); pressing the sleep button again, the internal and external displays are normal.

## Precautions

-`PNP0C0E` and `PNP0C0D` device names and paths must be consistent with ACPI.
