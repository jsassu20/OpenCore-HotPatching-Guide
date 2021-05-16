# Asus machine special patch

## Claim

-Check if the following `Method` and `Name` exist in ACPI, if not, ignore the content of this chapter.
  -`Name`: OSFG
  -`Method`: MSOS

## Special rename

PNLF renamed XNLF

```text
Find: 504E4C46
Replace: 584E4C46
```

There is a variable `PNLF` in the DSDT of some Asus machines. If `PNLF` and the brightness patch have the same name, there may be conflicts. Use the above name change to avoid it.

## Special Patch

-***SSDT-OCWork-asus***
  -Most Asus machines have a `MSOS` method. The ``MSOS`` method assigns a value to `OSFG` and returns the current state value. This [current state value] determines the working mode of the machine. For example, only when `MSOS` >= `0x0100`, the ACPI brightness shortcut key method will work. By default, `MSOS` is locked to `OSME`. **This patch** changes ``MSOS`` by changing `OSME`. For details of the `MSOS` method, please refer to the `Method (MSOS...` of DSDT).
    -`MSOS` >= `0x0100`, win8 mode, brightness shortcut keys work
  -The return value of ``MSOS`` depends on the operating system itself. You must use **operating system patch** or use **this patch** in a black apple state to meet specific requirements.
