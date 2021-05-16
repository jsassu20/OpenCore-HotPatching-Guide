# Comprehensive patch

## Description

-By renaming `_PTS` and `_WAK`, adding comprehensive patches and its extended patches, some problems in the sleep or wake-up process of some machines have been solved.

-Comprehensive patch is a framework which includes:
  -Shield the independent display interface `_ON`, `_OFF`.
  -6 extension patch interfaces `EXT1`, `EXT2`, `EXT3`, `EXT4`, `EXT5` and `EXT6`.
  -Define mandatory sleep transfer parameters `FNOK` and `MODE`, see "PNP0C0E Sleep Correction Method" for details.
  -Define the debugging parameters `TPTS` and `TWAK`, which are used to detect and track the changes of `Arg0` during sleep and wake-up. For example, add the following code to the brightness shortcut patch:

    ```Swift
    ...
    /* A key: */
    \RMDT.P2 ("ABCD-_PTS-Arg0=", \_SB.PCI9.TPTS)
    \RMDT.P2 ("ABCD-_WAK-Arg0=", \_SB.PCI9.TWAK)
    ...
    ```

    When you press the brightness shortcut key, you can see the value of `Arg0` after the previous sleep and wake-up on the console.

    Note: To debug ACPI, you need to install the driver ACPIDebug.kext, add the patch SSDT-RMDT, and customize the debug patch. See "ACPIDebug" for specific methods.

## Rename

To use the integrated patch, you must rename `_PTS` and `_WAK`. Choose the correct name change based on the original DSDT content, such as:

-`_PTS` to `ZPTS(1,N)`:

  ```Swift
    Method (_PTS, 1, NotSerialized) /* _PTS: Prepare To Sleep */
    {
  ```

-`_WAK` to `ZWAK(1,N)`:

  ```Swift
    Method (_WAK, 1, NotSerialized) /* _WAK: Wake */
    {
  ```

-`_PTS` to `ZPTS(1,S)`:

  ```Swift
    Method (_PTS, 1, Serialized) /* _PTS: Prepare To Sleep */
    {
  ```

-`_WAK` to `ZWAK(1,S)`:

  ```Swift
    Method (_WAK, 1, Serialized) /* _WAK: Wake */
    {
  ```

If `_TTS` exists in the DSDT, it needs to be renamed; if it does not exist, there is no need to rename it. Choose the correct name change based on the original DSDT content, such as:

-`_TTS` to `ZTTS(1,N)`:

  ```Swift
    Method (_TTS, 1, NotSerialized) /* _WAK: Wake */
    {
  ```

-`_TTS` to `ZTTS(1,S)`:

  ```Swift
    Method (_TTS, 1, Serialized) /* _WAK: Wake */
    {
  ```


## Patch

-***SSDT-PTSWAKTTS***-Comprehensive patch.

-***SSDT-EXT1-FixShutdown*** —— `EXT1` extension patch. Fix the problem that the XHC controller changes from shutdown to restart. The principle is to set `XHC.PMEE` to 0 when the parameter passed in `_PTS` is `5`. This patch has the same effect as Clover's `FixShutdown`. Some XPS / ThinkPad machines will need this patch.

-***SSDT-EXT3-WakeScreen*** —— `EXT3` extension patch. Solve the problem that some machines need to press any key to turn on the screen after waking up. When using it, check whether the device name and path of `PNP0C0D` are already in the patch file, such as `_SB.PCI0.LPCB.LID0`. If it does not exist, add it yourself.

-***SSDT-EXT5-TP-LED*** —— `EXT5` extension patch. Solved the problem that the breathing light on side A and the breathing light of the power button did not return to normal after the ThinkPad machine was awakened; fixed the problem that the status of the <kbd>F4</kbd> microphone indicator was abnormal after waking up on the old ThinkPad model.

## Note

Patches with the same extension name cannot be used at the same time. If there are requirements for simultaneous use, they must be combined.
