##Operating system patches

### Description

- **Operating System Patch** is used to remove restrictions on certain parts of the system. **Operating system patches** are generally not recommended. For components that are restricted by the system and cannot work normally, customize patches according to the specific conditions of ACPI.

- For a brief description of **OS patch**, please refer to the appendix "Origin of OS Patch"

## Rename

####OSID to XSID

```
Find: 4F534944
Replace: 58534944
```

####_OSI to XOSI

```
Find: 5F4F5349
Replace: 584F5349
```


*When there is an OSID in DSDT [dell notebook, etc.], it is required to use both `OSID to XSID` and` _OSI to XOSI`, and `OSID to XSID` before` _OSI to XOSI`. Otherwise, use only `_OSI to XOSI`.*

  
### Patch: 

*SSDT-OC-XOSI*

```
    Method (XOSI, 1)
    {
        If (_OSI ("Darwin"))
        {
            If (Arg0 == // "Windows 2009" // = win7, Win Server 2008 R2
                        // "Windows 2012" // = Win8, Win Server 2012
                        // "Windows 2013" // = win8.1
                        "Windows 2015" // = Win10
                        // "Windows 2016" // = Win10 version 1607
                        // "Windows 2017" // = Win10 version 1703
                        // "Windows 2017.2" // = Win10 version 1709
                        // "Windows 2018" // = Win10 version 1803
                        // "Windows 2018.2" // = Win10 version 1809
                        // "Windows 2018" // = Win10 version 1903
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

## USE

- **Maximum**

 - For a single system, you can set the operating system parameters to the maximum allowed by DSDT. For example: The maximum value of DSDT is "Windows 2018". Set Arg0 == "Windows 2018". Usually, Arg0 == "Windows 2013" and above can lift the system restrictions on components. **Note**: *Operating System Patch* is not recommended for single system.

- **Match Value**

 - For dual systems, set the operating system parameters to be the same as the Windows system version. For example: Windows system is win7, set Arg0 == "Windows 2009".



## Attachment: Origin of operating system patches

### Description

- When the system is loaded, ACPI's `_OSI` will receive a parameter. Different systems receive different parameters and ACPI executes different instructions. For example, the system is **Win7**, this parameter is `Windows 2009`, the system is **Win8**, and this parameter is` Windows 2012`. Such as:

```
  If ((_OSI ("Windows 2009") || _OSI ("Windows 2013")))
  {
      OperationRegion (PCF0, SystemMemory, 0xF0100000, 0x0200)
      Field (PCF0, ByteAcc, NoLock, Preserve)
      {
          HVD0, 32,
          Offset (0x160),
          TPR0, 8
      }
  
  }
  
  Method (_INI, 0, Serialized) // _INI: Initialize
  {
      OSYS = 0x07D0
      If (CondRefOf (\ _OSI))
      {
          If (_OSI ("Windows 2001"))
          {
              OSYS = 0x07D1
          }
  
          If (_OSI ("Windows 2001 SP1"))
          {
              OSYS = 0x07D1
          }
  
          If (_OSI ("Windows 2013"))
          {
              OSYS = 0x07DD
          }
  
          If (_OSI ("Windows 2015"))
          {
            OSYS = 0x07DF
          }
      }
  }
```

  ACPI also defines `OSYS`. The relationship between `OSYS` and the above parameters is as follows:

  `OSYS = 0x07D9`: Win7 system, ie` Windows 2009`.

  `OSYS = 0x07DC`: Win8 system, ie` Windows 2012`.

  `OSYS = 0x07DD`: Win8.1 system, ie` Windows 2013`.

  `OSYS = 0x07DF`: Win10 system, ie` Windows 2015`.

  `OSYS = 0x07E0`: Win10 1607, which is` Windows 2016`.

  `OSYS = 0x07E1`: Win10 1703, ie` Windows 2017`.

  `OSYS = 0x07E1`: Win10 1709, which is` Windows 2017.2`.

  `OSYS = 0x07E2`: Win10 1803, which is` Windows 2018`.

  `OSYS = 0x07E2`: Win10 1809, which is` Windows 2018.2`.

  `OSYS = 0x ????`: Win10 1903, ie `Windows 2019`.

  ...

- When the loaded system is not recognized by ACPI, `OSYS` is assigned a default value. This value varies from randomizer, some are` Linux`, some are `Windows2003`, and some are other values.

- Different operating systems support different hardware, such as `Win8` and above only support I2C devices.

- When loading macOS, the parameters accepted by `_OSI` will not be recognized by ACPI, and` OSYS` is assigned a default value. This default value is usually smaller than the value required by Win8. Obviously I2C cannot work. This requires patches to correct this error, and operating system patches originate from this.

- Some other components may also be related to `OSYS`.
