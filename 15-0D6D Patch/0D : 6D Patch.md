# 0D / 6D Patch

## Overview

- `_PRW` defines a component's wakeup method. Its `Return` is a data packet consisting of 2 or more bytes. For details on `_PRW`, see the ACPI specification.

- There are some parts, because their `_PRW` conflicts with macOS and the machine is immediately awakened as soon as it sleeps successfully. To solve the problem, patches must be applied to these components. The first byte of these `_PRW` packets is` 0D` or `6D`. Therefore, this kind of patch is called `0D / 6D patch`, also called` second wake up patch`, also called `wake up and wake up patch`. For the convenience of description, the following are collectively called `0D / 6D patches`.

- The second byte of the `_PRW` packet is mostly` 03` or `04`. Modifying this byte to` 0` completes the `0D / 6D patch`.

- Different machines may define `_PRW` differently, and the content and form of their data packets may also be diverse. The actual `0D / 6D patch` should be determined on a case- by- case basis. See description later.

- We expect that future versions of OpenCore will solve the `0D / 6D` problem.

### Components that may require `0D / 6D Patch`

- USB device
  - `ADR` address:` 0x001D0000`, part name: `EHC1`.
  - `ADR` address:` 0x001A0000`, part name: `EHC2`.
  - `ADR` address:` 0x00140000`, part name: `XHC`,` XHCI`, `XHC1`, etc.
  - `ADR` address:` 0x00140001`, part name: `XDCI`.
  - `ADR` address:` 0x00140003`, part name: `CNVW`.

- Ethernet

  - Before the 6th generation, the `ADR` address:` 0x00190000`, the part names: `GLAN`,` IGBE`, etc.
  - 6th generation and later, `ADR` address:` 0x001F0006`, part name: `GLAN`,` IGBE`, etc.

- Sound card

  - Before the 6th generation, the `ADR` address:` 0x001B0000`, the part names: `HDEF`,` AZAL`, etc.
  - 6th generation and later, `ADR` address:` 0x001F0003`, part name: `HDAS`,` AZAL`, etc.

** Note 1 **: The method of confirming the above parts by looking up the name is not reliable. A reliable method is to search for `ADR address`,` _PRW`.

   ** Note 2 **: Newly released machines may have new parts that require `0D / 6D Patch`.

## `_PRW` diversity and corresponding patch methods

- `Name`

  ```Swift
    Name (_PRW, Package (0x02)
    {
        0x0D, /* 可能是0x6D */
        0x03，/* 可能是0x04 */
        ...
    })
  ```

This type of `0D / 6D patch` is suitable for modifying` 0x03` (or `0x04`) to` 0x00` by binary renaming method. The package provides:

   - Name - 0D Rename .plist
     -`Name0D- 03` to` 00`
     -`Name0D- 04` to` 00`
   - Name - 6D renamed .plist
     -`Name6D- 03` to` 00`
     -`Name6D- 04` to` 00`

-One of `Method types`:` GPRW (UPRW) `

   `` `Swift
     Method (_PRW, 0, NotSerialized)
     {
       Return (GPRW (0x6D, 0x04)) / * or Return (UPRW (0x6D, 0x04)) * /
     }
   `` `

   This is mostly the case with newer machines. Just follow the usual method (renaming-patch). The package provides:

   - ***SSDT-GPRW*** (binary renamed data in patch file)
   - ***SSDT-UPRW*** (binary renamed data in the patch file)

-`Method type` two:` Scope`

```Swift
Scope(_SB.pci0.xhc)
{
    Method(_PRW, 0, NotSerialized)
    {
        If((Int(Local0) == 0x03))
        {
            Return(Package(0x02)
            {
                0x6d, 0x03
            }
        }
        If((Local0 == One))
        {
            Return(Package(0x02)
            do {
                0x6d, One
            }
        }
        Return(Package(0x02)
        {
            0x6d, Zero
        }
    }
}
```

This situation is not common. For the example case, use the binary rename ***Name6D- 03 to 00***. Try other forms of content yourself.

-`Name type`,` Method type` mixed method

   For most TP machines, the parts involving `0D / 6D patch` have both` Name` and `Method`. Use the respective type of patch. ** It should be noted that the binary renamed patches are not abused. Some components `_PRW` which do not require` 0D / 6D patch` may also be `0D` or` 6D`. To prevent this error, the System DSDT file should be extracted for verification and verification.

### Precautions

-The method described in this article works for Hotpatch.
-Where binary renaming is used, the `System DSDT` file should be extracted for verification.
