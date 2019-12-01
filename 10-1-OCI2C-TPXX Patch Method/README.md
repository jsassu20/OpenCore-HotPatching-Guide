# OCI2C- TPXX patch method

## Description

This method provides a solution for implementing Hotpatch patches on I2C devices. This method does not involve the specific process and details of the I2C patch. For more I2C aspects, see:

- @penghubingzhou: [https://www.penghubingzhou.cn] (https://www.penghubingzhou.cn)

- @ Bat.bat: [https://github.com/williambj1/VoodooI2C- PreRelease/blob/master/touchpad supplement.md # iv- check- ioregisteryexplorer- li- voodooi2c- whether loading normally] (github.com/williambj1/VoodooI2C- PreRelease/blob/master/touchpad supplement.md # iv- check- ioregisteryexplorer- li- voodooi2c- is loading normally)

- VoodooI2C official document: [https://voodooi2c.github.io/#GPIO%20Pinning/GPIO%20Pinning](https://voodooi2c.github.io/#GPIO%20Pinning/GPIO%20Pinning)

- VoodooI2C official support post [https://www.tonymacx86.com/threads/voodooi2c- help- and- support.243378/](https://www.tonymacx86.com/threads/voodooi2c- help- and- support. 243378 /)

- Q group: `837538729` (1 group is full),` 921143329` (2 group)

## Patch principle and process

- Forbid the original I2C device. See "Preset Variable Method" for details.

- Create a new I2C device `TPXX` and migrate all the contents of the original device to` TPXX`.

- Fixed `TPXX` related content:

  - Replace the original I2C device `name` with` TPXX`.

  - ** Fixed ** The `_STA` part is:

```Swift
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
```

  - ** Fixed ** The `relevant content 'of variables used when the original I2C device was banned to make it conform to the logical relationship.

  - ** Fixed ** relates to the "relevant content" of the system variable OSYS to make it conform to the logical relationship.

- Eliminate errors.

- I2C patch.

### Example (Dell Latitude 5480, device path: `\ _SB.PCI0.I2C1.TPD1`)

- Use the "Preset Variables Method" to disable `TPD1`.

```Swift
    Scope (\)
    {
        If (_OSI ("Darwin"))
        {
            SDS1 = 0
        }
    }
```

- Create a new device `TPXX` and migrate all the contents of the original` TPD1` to `TPXX`.

```Swift
    External (_SB.PCI0.I2C1, DeviceObj)
    Scope (_SB.PCI0.I2C1)
    {
        Device (TPXX)
        {
           Original TPD1 content
        }
    }
 ```

- Fixed `TPXX` content

  - Replace all `TPD1` with` TPXX`.
  
  - The `_STA` part of the patch is replaced with:
  
```Swift
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
```
  
  - Find `SDS1` (variable used when` TPD1` is disabled) and change the original `If (SDS1 ...)` to `If (one)`.
  
  - Find `OSYS` and delete (comment out) the following:
  
```Swift
// If (LLess (OSYS, 0x07DC))
// {
// SRXO (GPDI, One)
//}
```
  
    Note: When `OSYS` is less than` 0x07DC`, the I2C device does not work (`0x07DC` stands for Windows8).
  
- Added external references `External ...` to fix all errors.

- I2C patch (omitted)
