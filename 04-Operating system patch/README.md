# Operating system patch

## Description

-**Operating system patches** are used to lift the system's restrictions on certain components. Under normal circumstances, **"not recommended"** use **operating system patches**. For components that cannot work normally due to system limitations, patches should be customized according to ACPI's specific conditions.

-For a brief description of **operating system patches**, see the appendix "Origin of Operating System Patches".

## Rename

-**OSID to XSID**

  ```text
  Find: 4F534944
  Replace: 58534944
  ```

-**OSIF to XSIF**

  ```text
  Find: 4F534946
  Replace: 58534946
  ```

-**_OSI to XOSI**

  ```text
  Find: 5F4F5349
  Replace: 584F5349
  ```

  Search for `OSI` in the original DSDT. If there are other fields containing `OSI` besides the `_OSI` function (such as `OSID` of Dell notebooks, `OSIF` of some ThinkPad and Lenovo notebooks), you must first add the right These include the renaming of `OSI` fields (such as `OSID to XSID`, `OSIF to XSIF`), and then adding `_OSI to XOSI`.
  If there is no other field containing `OSI` except for the `_OSI` function, just add `_OSI to XOSI` directly.

## Patch: ***SSDT-OC-XOSI***

```Swift
Method(XOSI, 1)
{
    If (_OSI ("Darwin"))
    {
        If (Arg0 == //"Windows 2009" // = win7, Win Server 2008 R2
                    //"Windows 2012" // = Win8, Win Server 2012
                    //"Windows 2013" // = win8.1
                    "Windows 2015" // = Win10
                    //"Windows 2016" // = Win10 version 1607
                    //"Windows 2017" // = Win10 version 1703
                    //"Windows 2017.2"// = Win10 version 1709
                    //"Windows 2018" // = Win10 version 1803
                    //"Windows 2018.2"// = Win10 version 1809
                    //"Windows 2018" // = Win10 version 1903
            )
        {
            Return (0xFFFFFFFF)
        }
        Else
        {
            Return (Zero)
        }
    }
    Else
    {
        Return (_OSI (Arg0))
    }
}
```

### Use

-**Maximum value**

  For a single system, you can set the operating system parameters to the maximum allowed by DSDT. For example, if the maximum value of DSDT is `Windows 2018`, set `Arg0 == "Windows 2018"`. Usually `Arg0 == "Windows 2013"` and above can lift the system's restrictions on components.

  **Note**: **Operating system patch** is not recommended for single system.

-**Match value**

  For dual systems, the operating system parameters set should be consistent with the Windows system version. For example: Windows system is win7, set Arg0 == "Windows 2009".

## Precautions

  -The `Method` of some machines uses a name similar to `_OSI` (such as `OSID` of `dell`). When it is located in `_SB` (less than 4 characters) and the full path appears, its binary data and `_OSI` is the same, resulting in an error when the name is changed by the operating system (`_OSI to XOSI`). In this case, it needs to be renamed to something else (such as `OSID to XSID`) before `_OSI to XOSI` to avoid errors.

## Attachment: the origin of the operating system patch

-When the system is loaded, ACPI's `_OSI` will receive a parameter. Different systems have different parameters and ACPI executes different commands. For example, if the system is **Win7**, this parameter is `Windows 2009`, and the system is **Win8**, this parameter is `Windows 2012`. Such as:

  ```Swift
  If ((_OSI ("Windows 2009") || _OSI ("Windows 2013")))
  {
      OperationRegion (PCF0, SystemMemory, 0xF0100000, 0x0200)
      Field (PCF0, ByteAcc, NoLock, Preserve)
      {
          HVD0, 32,
          Offset (0x160),
          TPR0, 8
      }
      ...
  }
  ...
  Method (_INI, 0, Serialized) /* _INI: Initialize */
  {
      OSYS = 0x07D0
      If (CondRefOf (\_OSI))
      {
          If (_OSI ("Windows 2001"))
          {
              OSYS = 0x07D1
          }

          If (_OSI ("Windows 2001 SP1"))
          {
              OSYS = 0x07D1
          }
          ...
          If (_OSI ("Windows 2013"))
          {
              OSYS = 0x07DD
          }

          If (_OSI ("Windows 2015"))
          {
              OSYS = 0x07DF
          }
          ...
      }
  }
  ```

  ACPI also defines `OSYS`, the relationship between `OSYS` and the above parameters is as follows:

  -`OSYS = 0x07D9`: Win7 system, namely `Windows 2009`
  -`OSYS = 0x07DC`: Win8 system, namely `Windows 2012`
  -`OSYS = 0x07DD`: Win8.1 system, namely `Windows 2013`
  -`OSYS = 0x07DF`: Win10 system, namely `Windows 2015`
  -`OSYS = 0x07E0`: Win10 1607, which is `Windows 2016`
  -`OSYS = 0x07E1`: Win10 1703, which is `Windows 2017`
  -`OSYS = 0x07E1`: Win10 1709, which is `Windows 2017.2`
  -`OSYS = 0x07E2`: Win10 1803, which is `Windows 2018`
  -`OSYS = 0x07E2`: Win10 1809, which is `Windows 2018.2`
  -`OSYS = 0x????`: Win10 1903, which is `Windows 2019`
  -...

-When the loaded system is not recognized by ACPI, `OSYS` is assigned a default value. This value varies from machine to machine. Some represent `Linux`, some represent `Windows2003`, and some have other values.

-Different operating systems support different hardware. For example, I2C devices are only supported by `Win8`.

-When loading macOS, the parameters accepted by `_OSI` will not be recognized by ACPI, and `OSYS` is given a default value. This default value is usually less than the value required by Win8, obviously I2C cannot work. This requires a patch to correct this error, and the operating system patch comes from this.

-Some other components may also be related to `OSYS`.
