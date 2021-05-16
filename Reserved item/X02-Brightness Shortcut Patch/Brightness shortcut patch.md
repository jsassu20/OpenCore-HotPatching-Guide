# Brightness shortcut key patch

## Rename and patch

-Dell

  Renamed: `BRT6` to `XRT6` (dell-up/down)

  Patch: ***SSDT-BKeyBRT6-Dell***

  `Note`: The patch works properly and requires the operating system to be renamed patch `"Windows 2009"` or above or ***SSDT-OCWork-dell***.

-Thinkpad

  -Renamed 1: `_Q14` to `XQ14` (TP-up)
  -Rename 2: `_Q15` to `XQ15` (TP-down)
  -Patch 1: SSDT-BKeyQ14Q15-TP-`LPC`
  -Patch 2: SSDT-BKeyQ14Q15-TP-`LPCB`

  **Note**: Some Thinkpad machines are `LPC`, and some are `LPCB`. Please choose the appropriate patch file.

-Lenovo Xiaoxin IKB/IKBR/IWL

  -Renamed 1: `_Q12` to `XQ12` (LenovoAir-up)
  -Rename 2: `_Q11` to `XQ11` (LenovoAir-down)
  -Patch: ***SSDT-BKeyQ11Q12-LenAir***

### How to find the location of the brightness shortcut

-Normally, the brightness shortcut key is `_Qxx` of `EC`. This `_Qxx` contains `Notify(***, 0x86)` and `Notify(***, 0x87)` commands. If you can't find or confirm, search ACPI and find the location of `Notify(***, 0x86)` and `Notify(*** 0x87)`. According to the results of the query, refer to the sample to make your own patch file and rename it correctly.

-`Notify(***, 0x86)` corresponds to the up key, `Notify(***, 0x87)` corresponds to the down key

#### Remarks

Brightness shortcut keys can be assigned to two keys using keyboard mapping. See "PS2 Keyboard Mapping" for details.
