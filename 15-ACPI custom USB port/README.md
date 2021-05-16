# ACPI custom USB port

## Description

-This method realizes the customization of the USB port by modifying the ACPI file.
-This method needs to drop a certain ACPI file. Under normal circumstances, OpenCore **not recommended** to do so, custom USB ports generally use the ***Hackintool.app*** tool.
-This method is dedicated to fans.

## Scope of application

-XHC and its `_UPC` exist in a separate ACPI file
-This method is not applicable to devices where `_UPC` exists in DSDT (e.g. ASUS)

## `_UPC` Specification

```Swift
_UPC, Package ()
{
    xxxx,
    yyyy,
    0x00,
    0x00
}
```

### Explanation

1. **`xxxx`**
   -`0x00` means this port does not exist
   -Other values ​​(usually `0x0F`) represent the existence of this port

2. **`yyyy`**

   **`yyyy`** defines the port type, refer to the following table

   | **`yyyy`** | Port Type |
   | :------: | ----------------------------- |
   | `0x00` | USB Type `A` |
   | `0x01` | USB `Mini-AB` |
   | `0x02` | USB Smart Card |
   | `0x03` | USB 3 Standard Type `A` |
   | `0x04` | USB 3 Standard Type `B` |
   | `0x05` | USB 3 `Micro-B` |
   | `0x06` | USB 3 `Micro-AB` |
   | `0x07` | USB 3 `Power-B` |
   | `0x08` | USB Type `C` **(Only USB 2)** |
   | `0x09` | USB Type `C` **(with diverter)** |
   | `0x0A` | USB Type `C` **(without diverter)** |
   | `0xFF` | Built-in |

   > If the front and back of the USB-C are plugged into the same port in Hackintool, it means that the port has a redirector
   >
   > Conversely, if two ports are occupied on both sides, it means that there is no diverter

## USB customization process

-Clear patches, drivers, etc. of other customization methods.

-drop ACPI file

  -Confirm XHC and include the ACPI file of `_UPC`
    > Such as ***SSDT-2-xh_OEMBD.aml*** of dell5480
    >
    > Such as ***SSDT-8-CB-01.aml*** of Xiaoxin PRO13 (i5) (the machine without independent display is ***SSDT-6-CB-01.aml***)

  -`config\ACPI\Delete\` drop ACPI files in `TableLength` (decimal) and `TableSignature` methods. Such as:

    **dell5480**: **`TableLength`** = `2001`, **`TableSignature`** = `53534454` (SSDT)

    **Xiaoxin PRO13 (i5)**: **`TableLength`** = `12565`, **`TableSignature`** = `53534454` (SSDT)
    
    **TableLength** can be obtained from the `Length` at the header of the aml file.

-Customize SSDT patch file

  -Drag the original ACPI file that needs to be dropped to the desktop, **recommendation:**

    -Save as `.asl / .dsl` format
    -Modify the file name. Such as: ***SSDT-xh_OEMBD_XHC.dsl***, ***SSDT-CB-01_XHC.dsl***
    -Modify the `OEM Table ID` in the file to your favorite name.
    -Eliminate errors.

  -Add the following code to the front end of `_UPC` of all ports in the SSDT file:

    ```Swift
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
        /* The following is the original content */
        ...
    }
    ```

  -Customize the USB port according to the `_UPC` specification. That is, the values ​​of xxxx and yyyy are corrected.

    -If the port does not exist
      -**`xxxx`** = `0x00`
      -**`yyyy`** = `0x00`
    -If the port exists
      -**`xxxx`** = `0xFF`
      -**`yyyy`**

    > Refer to the table above
  
  -Debug, compile, and put patch files into `ACPI`, add patch list.

### Reference example

-***SSDT-xh_OEMBD_XHC***
-***SSDT-CB-01_XHC***
