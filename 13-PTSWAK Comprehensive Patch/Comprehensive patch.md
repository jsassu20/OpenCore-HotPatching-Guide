# Comprehensive patch

## Description

By renaming `_PTS` and` _WAK`, and adding a comprehensive patch and its extension patches, some problems during the sleep or wakeup process of some machines are solved.

**OpenCore does not recommend this**.

The comprehensive patch provides a framework that integrates four extension patch interfaces (`EXT1`,` EXT2`, `EXT3`, and` EXT4`), and defines the interface parameters of "PNP0C0E Forced Sleep". The patch itself did not break anything in the original ACPI. All patch work is done in the `Extended Patch`.

## Rename

`_PTS` and` _WAK` must be renamed to use a comprehensive patch. Choose the correct rename based on the original DSDT content, such as:

- `_PTS` to `ZPTS(1,N)`:

  ```Swift
    Method (_PTS, 1, NotSerialized)  /* _PTS: Prepare To Sleep */
    {
  ```

- `_WAK` to `ZWAK(1,N)`:

  ```Swift
    Method (_WAK, 1, NotSerialized)  /* _WAK: Wake */
    {
  ```

- `_PTS` to `ZPTS(1,S)`:

  ```Swift
    Method (_PTS, 1, Serialized)  /* _PTS: Prepare To Sleep */
    {
  ```

- `_WAK` to `ZWAK(1,S)`:

  ```Swift
    Method (_WAK, 1, Serialized)  /* _WAK: Wake */
    {
  ```

## 补丁

- ***SSDT-PTSWAK*** —— Comprehensive Patch。

- ***SSDT-EXT3-LedReset-TP*** — `EXT3` extension patch. Solve the problem that the breathing light does not return to normal after the TP machine wakes up。

- ***SSDT-EXT4-WakeScreen*** — `EXT4` extension patch. Solve the problem that some machines need to press any key to light up the screen after waking up. When using, you should inquire whether the `PNP0C0D` device name and path already exist in the patch file, such as` _SB.PCI0.LPCB.LID0`. If not, add it yourself.

## Note

Patches with the same extension name cannot be used simultaneously. If there are requirements for simultaneous use, they must be combined and used.
