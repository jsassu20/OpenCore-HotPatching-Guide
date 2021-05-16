# Preset variable method

## Description

The so-called `preset variable method` is to pre-assign some ACPI variables (`Name` type and `FieldUnitObj` type) to achieve the purpose of initialization. [Although these variables have been assigned at the time of definition, they have not changed before the `Method` calls them. 】

Correcting these variables in `Scope (\)` through third-party patch files can achieve our expected patch effect.

### Example 1

Suppose you want to ban a device. The usual method is to return the device's `_STA` to `Zero`.

Some original text:

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

It can be seen that as long as `SDS1` is not equal to `0x07`, `_STA` can return `Zero`. Use the **preset variable method** as follows:

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

In some Dell machines, the breathing light is "`off`" during sleep, and it is hoped to change to the `breathing state`. The study found that: Dell machines have a working status control. When working in Win8, the breathing light is "out"; when working in Win7, the breathing light is in a breathing state. In addition, when there is no operating system patch, this working state control is out of control, and the brightness shortcut key patch becomes invalid at the same time.

**Note**: The working status is different from the operating system.

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

According to the ***preset variable method***, set the Dell working status to Win7. Patch file: ***SSDT-OCWork-dell***

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

The official patch ***SSDT-AWAC*** is to force RTC to be enabled, applicable to some 300 series machines. When using it, please make sure that there is no `_INI` under `\_SB`, and there is `STAS` in the `_STA` method under RTC device.

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

When I2C patching, it may be necessary to enable GPIO. The `GPIO patch collection` of `OCI2C-TPXX patch method` adopts the `preset variable method`. Such as: ***SSDT-OCGPI0-GPEN***, ***SSDT-OCGPI0-GPHD***, ***SSDT-OCGPI0-SBRG***, etc.
