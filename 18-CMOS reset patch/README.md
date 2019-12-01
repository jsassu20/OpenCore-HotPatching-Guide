# CMOS reset patch

### Description

- "Power-on self-test error" occurs when some machines are shut down or restarted, which is caused by the CMOS being reset.
- When using clover, check `ACPI \ FixRTC` to solve the above problem.
- When using OpenCore, the following solutions are officially provided, see ***Sample.plist***:
   - Install ** RTCMemoryFixup.kext **
   - `Kernel \ Patch` patch: __ZN11BCM5701Enet14getAdapterInfoEv **
- This chapter provides a SSDT patching method to solve the above problems. This SSDT patch is essentially a counterfeit RTC. See Preset Variables and Counterfeit Devices.

### solution

- Delete the <u> interrupt number </ u> of the RTC [PNP0B00] part `_CRS`.

  ```
  Device (RTC)
  {
  		Name (_HID, EisaId ("PNP0B00"))
  		Name (_CRS, ResourceTemplate ()
  		{
  					IO (Decode16,
  							0x0070,
  							0x0070,
  							0x01, 
  							0x08,     // 或者0x02,同ACPI一致
  							)
  					IRQNoFlags () // 删除此行
  							{8}       // 删除此行
  		})
  }
  ```

### Patch: SSDT-RTC0-NoFlags

- Disable original parts: **RTC**
   - If **RTC** does not exist `_STA`, use the following method to disable **RTC**.
  
    ```
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
  
  - If `_STA` exists in **RTC**, disable **RTC** using the preset variable method. The variable in the example is `STAS`. When using it, pay attention to the effect of` STAS` on other devices and components.
  
    ```
        External (STAS, FieldUnitObj)
        Scope (\)
        {
            If (_OSI ("Darwin"))
            {
                STAS = 2
            }
        }
    ```
- Counterfeit ** RTC0 **, see sample.

### Attention

- The device name and path in the patch should be the same as the original ACPI.

- If the machine itself has RTC disabled for some reason, it needs to be faked to work properly. In this case, there is a POST error. Delete the interruption number of the fake patch:

  ```
  					IRQNoFlags () // 删除此行
  							{8}       // 删除此行
  ```


** Thanks ** @Chic Cheung, @Noctis for your hard work!