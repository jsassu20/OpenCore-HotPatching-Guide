# Dell machine special patch

## Claim

-Check if the following `Device` and `Method` exist in ACPI, and the names match, otherwise ignore the content of this chapter.
  -`Device`: ECDV【PNP0C09】
  -`Device`: LID0【PNP0C0D】
  -`Method`: OSID
  -`Method`: BTNV

## Special rename

PNLF renamed XNLF

```text
Find: 504E4C46
Replace: 584E4C46
```

There is a variable `PNLF` in the DSDT of some Dell machines. If `PNLF` and the brightness patch have the same name, there may be conflicts. Use the above name change to avoid it.

## Special Patch

-***SSDT-OCWork-dell***
  -Most Dell machines have the `OSID` method. The `OSID` method includes two variables, `ACOS` and `ACOS`, which determine the working mode of the machine. For example, only when `ACOS` >= `0x20`, ACPI's brightness shortcut key method will work. Listed below are the relationships between two variables and working modes. For details of the `OSID` method, please refer to the `Method (OSID...` of DSDT).
    -`ACOS` >= `0x20`, brightness shortcut key works
    -`ACOS` = `0x80`, `ACSE` = 0 is win7 mode, in this mode, the breathing light flashes during sleep
    -`ACOS` = `0x80`, `ACSE` = 1 is win8 mode, in this mode, the breathing light is off during sleep
  -The specific content of the two variables in the `OSID` method depends on the operating system itself. You must use **operating system patch** or use **this patch** in a black apple state to change these two variables to meet specific requirements.

-Fix the patch combination of Fn+Insert function key
  
  -***SSDT-PTSWAK*** See "PTSWAK Comprehensive Extension Patch"
  -***SSDT-EXT3-WakeScreen*** See "PTSWAK Comprehensive Extension Patch"
  -***SSDT-LIDpatch*** See "PNP0C0E Sleep Correction Method"
  -***SSDT-FnInsert_BTNV-dell*** See "PNP0C0E Sleep Correction Method"
