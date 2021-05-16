## ACPIDebug Instructions

#### Description

By adding debugging code to the ***SSDT-xxxx*** patch, you can see the working process of the patch or ACPI on the console for debugging the patch.

#### Drive

- ***ACPIDebug.kext***

   Install ***ACPIDebug.kext*** to OC \ Kexts and add the driver list.

#### Patch

- ***SSDT-RMDT***- Basic Patch

- *** SSDT- TEST- NEVT- dell *** —— Dell machine detection Fn function key patch sample

   Renamed: NEVT to XEVT

   `` `
   // Find: 4E 45 56 54 00
   // Replace: 58 45 56 54 00
   `` `

   Add ***SSDT-RMDT***, ***SSDT-TEST-NEVT-dell*** to OC \ ACPI, and add a patch list.

#### Debug

- Open <u> Control Panel </ u> and search for "AAA001". See ***SSDT-TEST-NEVT- dell***.
- Press a function key and observe the response of the console.

#### Remarks

Debug debugging code can be diversified, such as: \ RMDT.P1, \ RMDT.P2, \ RMDT.P3, etc. See ***SSDT-RMDT.dsl*** for details.

##### The above main content comes from @RehabMan

