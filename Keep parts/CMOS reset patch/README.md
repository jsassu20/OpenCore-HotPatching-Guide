# CMOS reset patch

## Description

-Some machines will appear **"Power-on self-test error"** when shutting down or restarting, which is caused by the resetting of CMOS.
-When using Clover, check `ACPI\FixRTC` to solve the above problems.
-When using OpenCore, the following solutions are officially provided, see ***Sample.plist***:
  -Install **RTCMemoryFixup.kext**
  -`Kernel\Patch` patch: **__ZN11BCM5701Enet14getAdapterInfoEv**
-This chapter provides an SSDT patch method to solve the above problems. This SSDT patch is essentially a counterfeit RTC, see "Preset Variable Method" and "Counterfeit Equipment".

## solution

-Delete the **interrupt number** of **RTC `PNP0B00`** part `_CRS`.

  ```Swift
  Device (RTC)
  {
      Name (_HID, EisaId ("PNP0B00"))
      Name (_CRS, ResourceTemplate ()
      {
          IO (Decode16,
              0x0070,
              0x0070,
              0x01,
              0x08, /* or 0x02, experimentally determined */
              )
          IRQNoFlags () /* delete this line */
              {8} /* Delete this line */
      })
  }
  ```

## Patch: SSDT-RTC0-NoFlags

-Disable original parts: **RTC**
  -If **RTC** does not exist `_STA`, use the following method to disable **RTC**:
  
    ```Swift
    External(_SB.PCI0.LPCB.RTC, DeviceObj)
    Scope (_SB.PCI0.LPCB.RTC)
    {
        Method (_STA, 0, NotSerialized)
        {
            If (_OSI ("Darwin"))
            {
                Return (Zero)
            }
            Else
            {
                Return (0x0F)
            }
        }
    }
    ```
  
  -If **RTC** exists `_STA`, use the preset variable method to disable **RTC**. The variable in the example is `STAS`, and attention should be paid to the influence of `STAS` on other equipment and components when using it.
  
    ```Swift
    External (STAS, FieldUnitObj)
    Scope (\)
    {
        If (_OSI ("Darwin"))
        {
            STAS = 2
        }
    }
    ```

-Counterfeit **RTC0**, see catalog.

## Note

-The device name and path in the patch should be consistent with the original ACPI.

-If the machine itself disables RTC for some reason, it needs to counterfeit RTC to work normally. In this case, **"Power-on self-check error"** appears, just delete the interrupt number of the counterfeit patch:

  ```Swift
    IRQNoFlags () /* delete this line */
        {8} /* Delete this line */
  ```

**Thanks** @Chic Cheung, @Noctis for your hard work!
