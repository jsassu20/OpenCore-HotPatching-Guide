# Prohibit PCI devices

## Description

-In some cases, we want to disable a PCI device. For example, the SD card of the PCI bus is usually unable to drive, even if it is driven, it can hardly work normally. In this case, we can disable this device by customizing the SSDT patch.
-These devices have the following characteristics:
  -It is a **child device** of a PCI **parent device**
  -**Parent device** defines some variables of type `PCI_Config` ** or `SystemMemory`**, where the bit `D4` of the offset 0x55 data is the device operating attribute
  -**Sub Device** Address: `Name (_ADR, Zero)`

## Device name

-The name of the **child device** of the newer machine is **`PXSX`**; the name of the **parent device** is **`RP01`**, **`RP02`**, **`RP03` **...Wait.
-Early Thinkpad machines **child device** name is **`SLOT`** or **none**; **parent device** name is **`EXP1`**, **`EXP2`**, **`EXP3`**...etc.
-Other machines may have other names.
-The built-in wireless network card of the notebook belongs to such a device.

## SSDT disable patch example

-The SD card of dell Latitude5480 is a PCI device, the device path: `_SB.PCI0.RP01.PXSX`

-Patch file: ***SSDT-RP01.PXSX-disbale***

  ```Swift
  External (_SB.PCI0.RP01, DeviceObj)
  Scope (_SB.PCI0.RP01)
  {
      OperationRegion (DE01, PCI_Config, 0x50, 0x01)
      Field (DE01, AnyAcc, NoLock, Preserve)
      {
              , 4,
          DDDD, 1
      }
  //possible start
  Method (_STA, 0, Serialized)
  {
  If (_OSI ("Darwin"))
  {
  Return (Zero)
  }
  }
  //possible end
  }
  Scope (\)
  {
      If (_OSI ("Darwin"))
      {
          \_SB.PCI0.RP01.DDDD = One
      }
  }
  ```

## Note

-If the **parent device** has multiple **child devices**, please **use this method with caution**.
-When using, please replace `RP01` in the example with the name of the **parent device** of the disabled device, refer to the example.
-If the disabled device already includes the `_STA` method, ignore the content from *possible start* to *possible end*, see the example comments.
-This method does not release the device from the PCI channel.

## Thanks

-@哞🌈
