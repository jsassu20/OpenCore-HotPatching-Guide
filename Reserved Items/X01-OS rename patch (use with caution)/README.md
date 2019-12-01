# Operating system renamed patch (`Use with caution`)

## `Special note`

- Normally, no operating system rename patch is required.
- For some devices (such as I2C devices) that cannot work normally due to operating system restrictions, try to apply custom patches based on their own ACPI characteristics.
- Use of operating system patches is a last resort. Please use it with caution.

## Operating system patches

See attachment "Origin of Operating System Patch"; the principle and method of renaming see "ACPI Binary Rename".

## `Operating System Renamed Patch`

- "Windows 2009" Renamed Patch- Win7 Patch
- "Windows 2012" renamed patch- Win8 patch
- "Windows 2013" renamed patch- Win8.1 patch
- `" Windows 2015 "renamed patch`—Win10 Build 10536 patch
- "Windows 2016" renamed patch- Win10 Build 15086 patch
- "Windows 2017" renamed patch- Win10 Build 16299 patch
- "Windows 2018" renamed patch- Win10 Build 17134 patch

### Example ("Windows 2013" renamed patch)

Before the name change:

`` `Swift
    If (_OSI ("Windows 2013"))
    {
        OSYS = 0x07DD
    }
`` `

After the name change:

`` `Swift
    If (_OSI ("Darwin"))
    {
        Noop
        Noop
        Noop
        Noop
        Noop
        Noop
        OSYS = 0x07DD
    }
`` `

### Correctly select `Operating System Rename Patch`

Which "operating system renamed patch" is correct is determined by the specific location of "Windows 20xx" of ACPI.

- Correct position: `" Windows 20xx "` at the `end` of a line of statements.
- Error location: `" Windows 20xx "` at the "head end" of a line of statement.

Such as:

`` `Swift
    If ((_OSI ("Windows 2009") || _OSI ("Windows 2013")))
    {
        ...
    }
`` `

In the example, selecting "" Windows 2009 "Rename Patch" will produce an error. Selecting "" Windows 2013 "Rename Patch" is correct.

In the following cases, the "" Windows 2013 "renamed patch" is also correct and effective.

Before the name change:

`` `Swift
    Name (WN81, "Windows 2013")
    Name (LINX, "Linux")
`` `

After the name change:

`` `Swift
    Name (WN81, "Darwin")
    Noop
    Noop
    Noop
    Noop
    Noop
    Noop
    Name (LINX, "Linux")
`` `

### Impact on dual systems

If a Windows system is installed in the machine and the Windows system is booted by OC, `operating system rename patch` may cause Windows to work abnormally. Replace other `operating system renamed patches` to avoid risks caused by interference between two systems.

- Avoidance principle: `Operating system renamed patch` does not equal` Windows system version number`. For example: The Windows system in a dual system is Win7. Then, the `operating system renamed patch` should select a correct renamed patch other than` Windows 2009 'renamed patch`.

#### Notes on use

- There are multiple "" Windows 20xx "" in DSDT.
- Extract the `System DSDT` file and search for` Darwin` to confirm that it has been renamed correctly.
- If you want the `Operating System Rename Patch` not in the sample, please add it yourself.

## Attachment: Origin of operating system patches

### Description

- When the system is loaded, ACPI's `_OSI` will receive a parameter. Different systems receive different parameters, and ACPI executes different instructions. For example, the system is ** Win7 **, this parameter is `Windows 2009`, the system is ** Win8 **, and this parameter is` Windows 2012`.

** Note **: There are multiple versions of Win10, corresponding to different parameters.

ACPI also defines `OSYS`. The relationship between `OSYS` and the above parameters is as follows:

`OSYS = 0x07D9`: Win7 system, ie` Windows 2009`.

`OSYS = 0x07DC`: Win8 system, ie` Windows 2012`.

`OSYS = 0x07DD`: Win8.1 system, ie` Windows 2013`.

`OSYS = 0x07DF`: The first version of Win10, which is` Windows 2015`.

...

- When the loaded system is not recognized by ACPI, `OSYS` is assigned a default value. This value varies from randomizer, some are` Linux`, some are `Windows2003`, and some are other values.

- Different operating systems support different hardware, such as `Win8` and above only support I2C devices.

- When loading macOS, the parameters accepted by `_OSI` will not be recognized by ACPI, and` OSYS` will be assigned a default value. This default value is usually smaller than the value required by Win8. Obviously I2C cannot work. This requires patches to correct this kind of error, and operating system patches originate from this.