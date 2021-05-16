# Counterfeit ambient light sensor

## Summary

Starting from `macOS Catalina`, notebook devices need to counterfeit the ambient light sensor `ALS0` to achieve brightness preservation and automatic brightness adjustment. It should be noted that only machines with real ambient light sensor hardware can achieve true automatic brightness adjustment.

## Instructions

There are two situations, the original `ACPI` has an ambient light sensor device interface and there is no ambient light sensor device interface. First search for `ACPI0008` in the original `ACPI`. If the relevant device can be found, usually `ALSD`, it means there is an ambient light sensor device interface, otherwise it means there is no ambient light sensor device interface.

### There is an ambient light sensor device interface

> Case

```swift
Device (ALSD)
{
  Name (_HID, "ACPI0008" /* Ambient Light Sensor Device */) // _HID: Hardware ID
  Method (_STA, 0, NotSerialized) // _STA: Status
  {
    If ((ALSE == 0x02))
    {
      Return (0x0B)
    }

    Return (Zero)
  }

  Method (_ALI, 0, NotSerialized) // _ALI: Ambient Light Illuminance
  {
    Return (((LHIH << 0x08) | LLOW))
  }

  Name (_ALR, Package (0x05) // _ALR: Ambient Light Response
  {
    Package (0x02)
    {
      0x46,
      Zero
    },

    Package (0x02)
    {
      0x49,
      0x0A
    },

    Package (0x02)
    {
      0x55,
      0x50
    },

    Package (0x02)
    {
      0x64,
      0x012C
    },

    Package (0x02)
    {
      0x96,
      0x03E8
    }
  })
}
```

In this case, you can use a preset variable to make its `_STA` method return `0x0B` to enable the original `ACPI` existing environmental sensor device, the method is as follows:

```swift
DefinitionBlock ("", "SSDT", 2, "OCLT", "ALSD", 0)
{
    External (ALSE, IntObj)

    Scope (_SB)
    {
        Method (_INI, 0, NotSerialized)
        {
            If (_OSI ("Darwin"))
            {
                ALSE = 2
            }
        }
    }
}
```

### There is no ambient light sensor device interface

In this case, we only need to fake an `ALS0` device, the method is as follows:

```swift
DefinitionBlock ("", "SSDT", 2, "ACDT", "ALS0", 0)
{
    Scope (_SB)
    {
        Device (ALS0)
        {
            Name (_HID, "ACPI0008")
            Name (_CID, "smc-als")
            Name (_ALI, 0x012C)
            Name (_ALR, Package (0x01)
            {
                Package (0x02)
                {
                    0x64,
                    0x012C
                }
            })
            Method (_STA, 0, NotSerialized)
            {
                If (_OSI ("Darwin"))
                {
                    Return (0x0F)
                }
                Else
                {
                    Return (Zero)
                }
            }
        }
    }
}
```

## Note

-Counterfeit devices are safe and effective in any case, even if there is an ambient light sensor device interface in the original `ACPI`, you can directly counterfeit `ALS0`.
-The modified `variable` may exist in multiple places. After modifying it, while achieving our expected results, it may affect other components.
-When there is an ambient light sensor device in the original `ACPI`, its name may not be `ALSD`, such as `ALS0`, but no other name has been found so far.
-When there is an ambient light sensor device in the original `ACPI`, if you want to use the preset variable method to forcibly enable it, you need to pay attention to whether there is `_SB.INI` in the original `ACPI`. If there is, please use the fake `ALS0` method.
