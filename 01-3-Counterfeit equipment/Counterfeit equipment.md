# Counterfeit equipment

## Summary

​ Among the many `SSDT` patches, a considerable number of patches can be classified as counterfeit device patches, such as:

-Some devices do not exist in ACPI, but the MAC system needs them. The correct description of these devices through the patch can load the device driver. Such as "OC-PNLF Injection Method", "Adding Missing Parts", "Counterfeit Ethernet", etc.

-EC issues. Such as "Counterfeit EC".

-For some special devices, the method of prohibiting the original device from counterfeiting it will bring us convenience in adjusting the patch. Such as "OCI2C-TPXX Patch Method".

-A certain device is disabled for some reason, but the MAC system needs it to work. See examples in `this chapter`.

-In most cases, the device can also be activated using "ACPI Binary Rename" or "Preset Variable Method".

## Counterfeit Equipment

-Features
  
  -The counterfeit device already exists in ACPI, and the code is relatively short, few, and independent.
  
  -The original device has the canonical `_HID` or `_CID`.
  
  -Even if the original device is not disabled, using a counterfeit device patch will not cause ACPI damage.
  
- Claim

  -The name of the counterfeit device is **different** from the original device name of ACPI.

  -The content of the patch is **same** as the main content of the original device.

  -The `_STA` part of the counterfeit patch should include the following to ensure that the windows system uses the original ACPI.

    ```
    Method (_STA, 0, NotSerialized)
    {
    If (_OSI ("Darwin"))
    {
    ......
    Return (0x0F)
    }
    Else
    {
    Return (Zero)
    }
    }
    ```
  
-Example
 
 -***SSDT-PS2N*** — Counterfeit point input device, see "Emergency Solution PS2N for Keyboard Cannot Input"
  
    -Original point input device name: PS2M, MOU, MSS*, etc.
  
    -_CID: PNP0F13, PNP0F03, PNP0F0E, PNP0F0B, PNP0F12
  -***SSDT-RTC0*** — Counterfeit RTC
  
    -Original device name: RTC
  
    -_HID: PNP0B00
  
    **Note**: The name of `LPCB` should be consistent with the original ACPI name.
