# OCI2C-TPXX patch method

## Description

This method provides a solution for implementing Hotpatch patches on I2C devices. This method does not involve the specific process and details of the I2C patch. For more information about I2C, see:

-@penghubingzhou: [https://www.penghubingzhou.cn](https://www.penghubingzhou.cn)
-@神楽小白(GZ小白):[https://blog.gzxiaobai.cn/](https://blog.gzxiaobai.cn/)
-@神楽小白(GZ小白) Touchpad hot patch example library: [https://github.com/GZXiaoBai/Hackintosh-TouchPad-Hotpatch](https://github.com/GZXiaoBai/Hackintosh-TouchPad- Hotpatch)
-VoodooI2C official document: [https://voodooi2c.github.io/#GPIO%20Pinning/GPIO%20Pinning](https://voodooi2c.github.io/#GPIO%20Pinning/GPIO%20Pinning)
-VoodooI2C official support post [https://www.tonymacx86.com/threads/voodooi2c-help-and-support.243378/](https://www.tonymacx86.com/threads/voodooi2c-help-and-support. 243378/)
-Q group: `837538729` (1 group is full), `921143329` (2 groups)

## Patch principle and process

-Prohibit the original I2C device. See "Binary Rename and Preset Variables" for details.

  ```Swift
  /*
   * GPI0 enable
   */
  DefinitionBlock("", "SSDT", 2, "OCLT", "GPI0", 0)
  {
      External(GPEN, FieldUnitObj)
      // External(GPHD, FieldUnitObj)
      Scope (\)
      {
          If (_OSI ("Darwin"))
          {
              GPEN = 1
              // GPHD = 2
          }
      }
  }
  ```

-Create a new I2C device `TPXX`, and migrate all the contents of the original device to `TPXX`.

-Amend the related content of `TPXX`:

  -Replace all the original I2C device `name` with `TPXX`

  -**Amendment** The `_STA` part is:

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

  -**Fixed** to prohibit the `related content` of the variables used in the original I2C device, so that it conforms to the logical relationship.

  -**Fix** the `relevant content` related to the operating system variable OSYS, so that it conforms to the logical relationship.

-Eliminate errors.

-I2C patch.

### Example (Dell Latitude 5480, device path: `\_SB.PCI0.I2C1.TPD1`)

-Use "Preset Variables Law" to prohibit `TPD1`.

  ```Swift
  Scope (\)
  {
      If (_OSI ("Darwin"))
      {
          SDS1 = 0
      }
  }
  ```

-Create a new device `TPXX`, and migrate all the contents of the original `TPD1` to `TPXX`.

  ```Swift
  External(_SB.PCI0.I2C1, DeviceObj)
  Scope (_SB.PCI0.I2C1)
  {
      Device (TPXX)
      {
         Original TPD1 content
      }
  }
  ```

-Modify the content of `TPXX`

  -Replace all `TPD1` with `TPXX`.
  
  -The `_STA` part of the patch is replaced with:
  
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
  
  -Find `SDS1` (variable used when `TPD1` is prohibited), and change the original `If (SDS1...)` to `If (one)`.
  
  -Find `OSYS` and delete (comment out) the following:
  
    ```Swift
    //If (LLess (OSYS, 0x07DC))
    //{
    // SRXO (GPDI, One)
    //}
    ```
  
    Note: When `OSYS` is less than `0x07DC`, the I2C device does not work (`0x07DC` stands for Windows8).
  
-Add external reference `External...` to fix all errors.

-I2C patch (omitted)
