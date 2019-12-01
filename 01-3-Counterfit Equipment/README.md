## Summary

Among many `SSDT` patches, a considerable number of patches can be summarized as counterfeit device patches, such as:

- Some devices do not exist in ACPI, but they are required by MAC systems. The correct description of these devices through patches can load device drivers. Such as "OC-PNLF injection method", "Add missing parts", "Fake Ethernet" and so on.

- EC issues. Such as "Counterfeit EC".

 - For some special devices, using the method of forbidding the original device from counterfeiting it will make it easier for us to adjust the patch. Such as "OCI2C-TPXX patch method".

 - A device was disabled for some reason, but the MAC system needs it to work. See `this chapter` for examples.

 - In most cases, the device can also be enabled using the ACPI Binary Rename or the Preset Variable Method.

## Counterfit Device

* Features

 - The counterfeit device already exists in ACPI, and the code is relatively short, few and independent.
 
 - The original device has a canonical `_HID` or` _CID`.
 
 - Even if the original device is not disabled, using the counterfeit device patch will not harm ACPI.
  
* Claim

 - Counterfeit device name is different from ACPI's original device name **.
 
 - The patch content is the same as the main content of the original device **.
 
 - The `_STA` part of the counterfeit patch should include the following to ensure that the windows system uses the original ACPI.

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
  
##Example

 - **SSDT-PS2N** 
 
     - Counterfeit point input device, see "Emergency Solution for Keyboard Cannot Input PS2N"

     - Origin point input device name: PS2M, MOU, MSS *, etc.
  
     -_CID: PNP0F13, PNP0F03, PNP0F0E, PNP0F0B, PNP0F12
   
- **SSDT-RTC0** 
   - Counterfeit RTC
   
   - Original equipment name - RTC
   
   - _HID: PNP0B00
  
- **NOTE:** The `LPCB` name should be the same as the original ACPI name.
  
