# HP special `0D6D` patch

-For related content of `0D/6D Patch`, please refer to "0D6D Patch"

-For some HP machines, the `_PRW` method of some parts of `ACPI` (related to `0D6D`) is as follows:

   ```swift
   Method (_PRW, 0, NotSerialized)
   {
       Local0 = Package (0x02)
       {
           Zero,
           Zero
       }
       Local0 [Zero] = 0x6D
       If ((USWE == One)) /* Note USWE */
       {
           Local0 [One] = 0x03
       }
       Return (Local0)
   }
   ```

   In this case, you can use the "Preset Variable Method" to complete the `0D/6D patch`, such as:

   ```swift
   Scope (\)
   {
       If (_OSI ("Darwin"))
       {
           USWE = 0
       }
   }
   ```

-Example: ***SSDT-0D6D-HP***

    ***SSDT-0D6D-HP*** is applicable to `HP 840 G3`, the patch fixes the `_PRW` return value of `XHC` and `GLAN`.

-Refer to sample files for other machines with similar situations.
