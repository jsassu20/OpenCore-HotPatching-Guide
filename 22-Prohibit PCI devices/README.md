## Disable PCI device

#### Description

- In some cases, we want to disable a PCI device. For example, the SD card of the PCI bus usually cannot be driven, and even if it is driven, it can hardly work normally. In this case, we can disable this device through a third-party SSDT patch.
- These devices have the following characteristics:
  - It is a **child device** of a PCI **parent device**
  - **Parent device** defines some variables of type `PCI_Config` [or` SystemMemory`], where the 55th bit is the device operation attribute
  - **Sub-device** Address: `Name (_ADR, Zero)`

#### Device name

- Newer machine's **child device** is named **PXSX**; **parent device** is named **RP01**, **RP02**, **RP03** ... etc.
- Early Thinkpad machines **child devices** are named **SLOT** or **none;** **parent devices** are named **EXP1**, **EXP2**, **EXP3**. ..Wait.
- Other machines may be other names.
- The laptop's built-in wireless network card belongs to such a device.

#### SSDT Disable Patch Example

- SD card of dell Latitude5480 belongs to PCI device, the device path is: `_SB.PCI0.RP01.PXSX`

- Patch files：***SSDT-RP01.PXSX-disbale*** 

```
      External (_SB.PCI0.RP01, DeviceObj)
      Scope (_SB.PCI0.RP01)
      {
          OperationRegion (DE01, PCI_Config, 0x50, 0x01)
          Field (DE01, AnyAcc, NoLock, Preserve)
          {
                  ,   1, 
                  ,   3, 
              DDDD,   1
          }
      }  
      Scope (\)
      {
          If (_OSI ("Darwin"))
          {
              \_SB.PCI0.RP01.DDDD = One
          }
      }
```

#### Note

- If **parent device** has multiple **child devices**, please use this method with caution**.
- Please replace `RP01` in the example with the name of the **parent device** of the disabled device. Refer to the example.


