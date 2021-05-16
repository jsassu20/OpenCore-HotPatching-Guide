# SSDT patch loading sequence

-Normally, the object of our SSDT patch is the machine's ACPI (DSDT or other SSDT files). These original ACPIs are loaded earlier than our SSDT patch. Therefore, our SSDT patch is **no order required** in the `Add` list.
-There is such a situation, when we define a `Device` device in an SSDT patch, and refer to this `Device` through `Scope` in another SSDT patch, then these two patches are in the `Add` list **There are order requirements**.

## Example

-Patch 1: **SSDT-XXXX-1.aml**
  
  ```Swift
  External (_SB.PCI0.LPCB, DeviceObj)
  Scope (_SB.PCI0.LPCB)
  {
      Device (XXXX)
      {
          Name (_HID, EisaId ("ABC1111"))
      }
  }
  ```
  
-Patch 2: **SSDT-XXXX-2.aml**

  ```Swift
  External (_SB.PCI0.LPCB.XXXX, DeviceObj)
  Scope (_SB.PCI0.LPCB.XXXX)
  {
        Method (YYYY, 0, NotSerialized)
       {
           /* do nothing */
       }
    }
  ```
  
-`Add` list requirements

  ```XML
  Item 1
            path <SSDT-XXXX-1.aml>
  Item 2
            path <SSDT-XXXX-2.aml>
  ```
