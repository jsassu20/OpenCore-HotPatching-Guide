# 0D/6D patch

## Overview

-`_PRW` defines the wake-up method of a component. The `Return` is a packet consisting of 2 or more bytes. For details of `_PRW`, refer to the ACPI specification.
-There are some components, because their `_PRW` conflicts with macOS, which causes the machine to wake up as soon as it successfully sleeps. In order to solve the problem, patches must be applied to these components. These components `_PRW` The first byte of the data packet is `0D` or `6D`. Therefore, this kind of patch is called `0D/6D patch`, also called `second wake-up patch`, also called `sleep-and-wake patch`. For the convenience of description, the following are collectively referred to as `0D/6D patch`.
-The second byte of the `_PRW` data packet is mostly `03` or `04`. Correcting this byte to `0` completes the `0D/6D patch`.
-Different machines may define different methods for `_PRW`, and the contents and forms of data packets may also be diversified. The actual `0D/6D patch` should be determined on a case-by-case basis. See the description below.
-We expect subsequent versions of OpenCore to solve the `0D/6D` problem.

### Parts that may require `0D/6D patch`

-USB device
  -`ADR` address: `0x001D0000`, part name: `EHC1`.
  -`ADR` address: `0x001A0000`, part name: `EHC2`.
  -`ADR` address: `0x00140000`, part name: `XHC`, `XHCI`, `XHC1` etc.
  -`ADR` address: `0x00140001`, part name: `XDCI`.
  -`ADR` address: `0x00140003`, part name: `CNVW`.

-Ethernet

  -Before the 6th generation, `ADR` address: `0x00190000`, part name: `GLAN`, `IGBE`, etc.
  -6th generation and later, `ADR` address: `0x001F0006`, part name: `GLAN`, `IGBE` etc.

-Sound card

  -Before the 6th generation, `ADR` address: `0x001B0000`, part name: `HDEF`, `AZAL`, etc.
  -6th generation and later, `ADR` address: `0x001F0003`, part name: `HDAS`, `AZAL` etc.

  **Note 1**: The method of confirming the above parts by looking up the name is not reliable. The reliable method is to search for `ADR address`, `_PRW`.

  **Note 2**: Newly released machines may have new parts that require `0D/6D patch`.

## The diversity of `_PRW` and the corresponding patch method

-`Name type`

  ```Swift
    Name (_PRW, Package (0x02)
    {
        0x0D, /* may be 0x6D */
        0x03, /* may be 0x04 */
        ...
    })
  ```

  This type of `0D/6D patch` is suitable for modifying `0x03` (or `0x04`) to `0x00` by using the binary renaming method. The package provides:

  -Name-0D renamed .plist
    -`Name0D-03` to `00`
    -`Name0D-04` to `00`
  -Name-6D renamed to .plist
    -`Name6D-03` to `00`
    -`Name6D-04` to `00`

-One of `Method type`: `GPRW(UPRW)`

  ```Swift
    Method (_PRW, 0, NotSerialized)
    {
      Return (GPRW (0x6D, 0x04)) /* or Return (UPRW (0x6D, 0x04)) */
    }
  ```

  Most newer machines fall into this situation. Follow the usual method (renaming-patch). The package provides:

  -***SSDT-GPRW*** (there is binary rename data in the patch file)
  -***SSDT-UPRW*** (there is binary rename data in the patch file)

-`Method type` two: `Scope`

  ```Swift
    Scope (_SB.PCI0.XHC)
    {
        Method (_PRW, 0, NotSerialized)
        {
            ...
            If ((Local0 == 0x03))
            {
                Return (Package (0x02)
                {
                    0x6D,
                    0x03
                })
            }
            If ((Local0 == One))
            {
                Return (Package (0x02)
                {
                    0x6D,
                    One
                })
            }
            Return (Package (0x02)
            {
                0x6D,
                Zero
            })
        }
    }
  ```

  This situation is not common. For the case of the example, use the binary name to change the name ***Name6D-03 to 00***. Try other forms of content on your own.

-`Name type`, `Method type` mixed method

  For most TP machines, the parts involved in `0D/6D patch` have both `Name type` and `Method type`. Just use the respective types of patches. **Note that **Binary renamed patches cannot be abused. Some components `_PRW` that do not require `0D/6D patch` may also be `0D` or `6D`. To prevent this kind of error, the `System DSDT` file should be extracted for verification and verification.

### Precautions

-The method described in this article is applicable to Hotpatch.
-For any binary renaming used, the `System DSDT` file should be extracted for verification.
