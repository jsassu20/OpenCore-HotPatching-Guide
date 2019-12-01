# Preset variable method

## Description

The so-called `preset variable method` is to pre-assign some ACPI variables (type of` Name` and `FieldUnitObj`) to achieve the purpose of initialization. [Although these variables were assigned values at the time of definition, they were not changed until they were called by `Method`. 】

Modifying these variables in Scope (\) through third-party patch files can achieve the expected patch effect.

### Example 1

If you want to ban a device. The usual method is to make `_STA` of the device return` Zero`.

Some text:

```Swift
    Method (_STA, 0, NotSerialized)
    {
        ECTP (Zero)
        If ((SDS1 == 0x07))
        {
            Return (0x0F)
        }
        Return (Zero)
    }
```

It can be seen that as long as `SDS1` is not equal to` 0x07`, `_STA` can return` Zero`. The ** preset variable method ** is used as follows:

```Swift
    Scope (\)
    {
        External (SDS1, FieldUnitObj)
        If (_OSI ("Darwin"))
        {
            SDS1 = 0
        }
    }
```

### Example 2

On some Dell machines, the breathing light "turns off" during sleep and wants to change to the "breathing state". The study found that the Dell machine has a working state control. When working in Win8, the breathing light is "off"; when working in Win7, the breathing light is in a "breathing state". In addition, in the absence of an operating system patch, the control of this working state is out of control, and the brightness shortcut key patch is invalidated at the same time.

** Note **: The working status is different from the operating system.

original:

```Swift
    Name (ACOS, Zero)
    Name (ACSE, Zero)
    ...
    If ((ACOS == Zero))
    {
        ACOS = One
        ACSE = Zero
        ......
            If (_OSI (WIN7))
            {
                ACOS = 0x80
            }
            If (_OSI (WIN8))
            {
                ACOS = 0x80
                ACSE = One
            }
        ...
    }
```

Set the Dell working status to Win7 according to the *** Preset Variable Method ***. Patch file: *** SSDT-OCWork-dell ***

```Swift
    External (_SB.ACOS, IntObj)
    External (_SB.ACSE, IntObj)
    Scope (\)
    {
        If (_OSI ("Darwin"))
        {
            \_SB.ACOS = 0x80
            \_SB.ACSE = Zero /*ACSE=0:win7;;ACSE=1:win8*/
        }
    }
```

### Example 3

The official patch *** SSDT-AWAC *** is forcibly enabling RTC and is applicable to some 300 series machines. When using, please make sure that there is no `_INI` under` \ _SB` and that the `_STA` method under RTC device has` STAS`.

```Swift
    External (STAS, IntObj)
    Scope (_SB)
    {
        Method (_INI, 0, NotSerialized)
        {
            If (_OSI ("Darwin"))
            {
                STAS = One
            }
        }
    }
```

### Example 4

When I2C patching, GPIO may need to be enabled. The `GPIO patch collection` of the` OCI2C-TPXX patch method` uses the `preset variable method`. Such as: *** SSDT-OCGPI0-GPEN ***, *** SSDT-OCGPI0-GPHD ***, *** SSDT-OCGPI0-SBRG *** and so on.
