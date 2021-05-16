# ThinkPad battery patch

## Description

-Please read `Attachment` "ThinkPad Battery System".
-Confirm that the path of `Main Battery` is `\_SB.PCI0.`**`LPC`**`.EC.BAT0` or `\_SB.PCI0.`**`LPCB`**`.EC.BAT0`, Not both, the content of this chapter is for reference only.
-The following is divided into **three cases** to explain how to use battery patch.

### Single battery system rename and patch

-Rename:
  -TP battery is basically renamed
  -TP battery `Mutex` set to `0` renamed
-Patch
  -`Main battery` patch - ***SSDT-OCBAT0-TP******

### Dual battery system with one physical battery renamed and patched

-Rename
  -TP battery is basically renamed
  -TP battery `Mutex` set to `0` renamed
  -`BAT1` disables renaming `_STA to XSTA`
  
    **Note**: Please use `Count`, `Skip`, `TableSignature` correctly, and verify that the location of `_STA to XSTA` is correct through system-DSDT
-Patch
  
  -`Main battery` patch - ***SSDT-OCBAT0-TP******
  -`BAT1` disable patch - ***SSDT-OCBAT1-disable-`LPC`*** [or ***SSDT-OCBAT1-disable-`LPCB`***】

### Dual battery system two physical batteries renamed and patched

-Rename
  -TP battery is basically renamed
  -TP battery `Mutex` set to `0` renamed
  -`Notify` renamed
-Patch
  -`Main battery` patch --***SSDT-OCBAT0-TP******
  -`BATC` patch-- ***SSDT-OCBATC-TP-`LPC`*** [or ***SSDT-OCBATC-TP-`LPCB`*** ***SSDT-OCBATC-TP-` _BIX`***】
  -`Notify` patch - ***SSDT-Notify-`LPC`*** [or ***SSDT-Notify-`LPCB`***】
  
    **note**:
  
    -When using the `BATC` patch, use the 7th generation + machine ***SSDT-OCBATC-TP-`_BIX`***
    -When selecting the `Notify` patch, you should **carefully** check the `_Q22`, `_Q24`, `_Q25`, `_Q4A`, `_Q4B`, `_Q4C`, `_Q4D`, `BATW`, Whether the content of `BFCC` is consistent with the corresponding content of the original `ACPI`, if not, please correct the corresponding content of the patch. For example, the content of `_Q4C` of the 3rd generation machine is different from the sample content; the 4th, 5th, 6th, and 7th generation machines do not have `_Q4C`; the 7th generation + machine has `BFCC`. and many more....... The sample file ***SSDT-Notify-`LPCB`*** is only for T580.
-Loading sequence
  -`Main battery` patch
  -`BATC` patch
  -`Notify` patch

## Precautions

-***SSDT-OCBAT0-TP****** is the `main battery` patch. When choosing, choose the corresponding patch according to the machine model.
-When selecting patches, pay attention to the difference between `LPC` and `LPCB`.
-Do you need `TP battery Mutex set to 0 and renamed`, try it yourself.

## `Notify` patch example [only `Method (_Q22` ... part])

> T580 Original

```Swift
Method (_Q22, 0, NotSerialized) /* _Qxx: EC Query, xx=0x00-0xFF */
{
    CLPM ()
    If (HB0A)
    {
        Notify (BAT0, 0x80) /* Status Change */
    }

    If (HB1A)
    {
        Notify (BAT1, 0x80) /* Status Change */
    }
}
```

> Rewrite

```swift
/*
 * For ACPI Patch:
 * _Q22 to XQ22:
 * Find: 5f51 3232
 * Replace: 5851 3232
 */
Method (_Q22, 0, NotSerialized) /* _Qxx: EC Query, xx=0x00-0xFF */
{
    If (_OSI ("Darwin"))
    {
        CLPM ()
        If (HB0A)
        {
            Notify (BATC, 0x80) /* Status Change */
        }

        If (HB1A)
        {
            Notify (BATC, 0x80) /* Status Change */
        }
    }
    Else
    {
        \_SB.PCI0.LPCB.EC.XQ22 ()
    }
}
```

See `Notify` patch for details - ***SSDT-Notify-`BFCC`***

> The verified machine is `ThinkPad T580`, the patch and rename are as follows:

-**SSDT-OCBAT0-TP_tx80_x1c6th**
-**SSDT-OCBATC-TP-`LPCB`**
-**SSDT-Notify-`LPCB`**
-**TP battery is basically renamed**
-**Notify renamed**

### Accessories: ThinkPad battery system

#### Overview

Thinkpad battery system is divided into single battery system and dual battery system.

-Dual battery system means that the machine is equipped with two batteries. Or, although the machine is equipped with only one battery, it provides the physical interface of the second battery and the corresponding ACPI. The second battery is optional and can be installed later. A machine with a dual battery system may have one battery or two batteries.
-Single battery system is the ACPI in which the machine is equipped with one battery and only one battery.

-For example, the battery structure of T470 and T470s is a dual battery system, and the battery structure of T470P is a single battery system. For another example, the T430 series is a dual battery system. The machine itself has only one battery, but the second battery can be installed through the optical drive.

#### Single and dual battery system judgment

-Dual battery system: both `BAT0` and `BAT1` exist in ACPI
-Single battery system: ACPI only has `BAT0`, no `BAT1`
