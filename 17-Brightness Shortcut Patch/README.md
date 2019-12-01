# Brightness shortcut patch

## Rename and Patch

- Thinkpad

  - Rename 1: `_Q14` to` XQ14` (TP-up)
  - Rename 2: `_Q15` to` XQ15` (TP-down)
  - Patch 1: SSDT-BKeyQ14Q15-TP-`LPC`
  - Patch 2: SSDT-BKeyQ14Q15-TP-`LPCB`

  ** Note **: Some Thinkpad machines are `LPC` and some are` LPCB`. Pay attention to the selection of appropriate patch files.

- Lenovo Xiaoxin IKB / IKBR / IWL

  - Rename 1: `_Q12` to` XQ12` (LenovoAir-up)
  - Rename 2: `_Q11` to` XQ11` (LenovoAir-down)
  - Patch: ***SSDT-BKeyQ11Q12-LenAir***
  
- Lenovo PRO13- IML

  - Rename 1: `_Q38` to` XQ38` (LenovoPRO13-up)
  - Rename 2: `_Q39` to` XQ39` (LenovoPRO13-down)
  - Patch: ***SSDT-BKeyQ38Q39-LenovoPRO13***

### How to find the location of the brightness shortcut

- In general, the brightness shortcut is `_Qxx` of` EC`. This `_Qxx` contains` Notify (***, 0x86) `and` Notify (***, 0x87) `instructions. If you can't find it or can't confirm, search ACPI to find the locations of `Notify (***, 0x86)` and `Notify (*** 0x87)`. Make your own patch file by referring to the sample based on the query result and rename it correctly.

- `Notify (***, 0x86)` corresponds to the up key, and `Notify (***, 0x87)` corresponds to the down key

#### Remarks

You can use the keyboard mapping method to specify two keys to implement the brightness adjustment function. See "PS2 Keyboard Mapping" for details.
