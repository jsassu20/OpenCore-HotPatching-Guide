## ACPI Custom USB Port

#### Description

- This method customizes the USB port by modifying the ACPI file.
- An ACPI file needs to be dropped during the operation of this method. Under normal circumstances, OpenCore **not recommended**, custom USB ports generally use the ***Hackintool.app*** tool.
- This method is dedicated to lovers.

#### Scope

- XHC and its `_UPC` exist in separate ACPI files.

#### `_UPC` specification

```
_UPC, Package ()
{
   	xxxx,//xxxx：0xFF表示该端口存在，0x00表示该端口不存在。
   	yyyy,//yyyy：0x00表示usb2.0，0x03表示usb3.0，0xFF表示内置，0x09表示typeC，其他值等。
   	0x00, 
  	0x00
}
```

For details on `_UPC`, see the ACPI specification.

#### USB Customization Process

- Remove patches, drivers, etc. from other customization methods.

- drop ACPI file

   - Confirm XHC and include ACPI files for `_UPC`. Such as dell5480's ***SSDT-2-xh_OEMBD.aml*** and Xiaoxin PRO13 (i5) 's ***SSDT-8-CB-01.aml***.

   - `config \ ACPI \ Block \` Drop ACPI files in `TableLength` (decimal) and` TableSignature`. Such as:

     **dell5480**: `TableLength` = 2001,` TableSignature` = 53534454 (SSDT)

     **Xiaoxin PRO13 (i5)**: `TableLength` = 12565,` TableSignature` = 53534454 (SSDT)

- Custom SSDT patch files

   - Drag the original ACPI file that needs to drop to the desktop, **Suggest:**

     - Save as DSL format.
     - Modify the file name. Such as: ***SSDT-2-xh_OEMBD_XHC.dsl*** 、 ***SSDT-8-CB-01_XHC.dsl***。 
- Modify the `OEM Table ID` in the file to your favorite name.
     - Eliminate errors.

   - Add the following code to the `_UPC` front end of all ports in the SSDT file:

    ```
    Method (_UPC, 0, NotSerialized) 
    {
    		If (_OSI ("Darwin"))
    		{
    				Return (Package ()
    				{
    						xxxx,
    						yyyy, 
    						0x00, 
    						0x00
    				})
    		}
    		//以下是原内容
    		......
    }
    ```

- Customize USB port according to `_UPC` specification. That is, the values of xxxx and yyyy are corrected.

     - If the port does not exist
       - xxxx = 0x00
       - yyyy = 0x00
     - If port exists
       - xxxx = 0xFF
       - yyyy
         - 0x00: USB2.0
         - 0x03: USB3.0
         - 0xFF: built-in USB device
         - 0x09: typeC
         - Other values, refer to ACPI specification
  
   - Troubleshoot, compile, put patch files in ACPI, add patch list.

#### Reference example

- ***SSDT-2-xh_OEMBD_XHC*** 
- ***SSDT-8-CB-01_XHC*** 

