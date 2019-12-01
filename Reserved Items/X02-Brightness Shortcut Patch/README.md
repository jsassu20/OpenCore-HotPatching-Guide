# Brightness shortcut patch

## Rename and Patch

- Dell

  Rename: `BRT6` to` XRT6` (dell- up / down)

  Patch: *** SSDT- BKeyBRT6- Dell ***

  `Note`: The patch work normally requires the operating system to rename the patch` "Windows 2009" `or above *** SSDT- OCWork- dell ***.

- Thinkpad

  - Rename 1: `_Q14` to` XQ14` (TP- up)
  - Rename 2: `_Q15` to` XQ15` (TP- down)
  - Patch 1: SSDT- BKeyQ14Q15- TP- `LPC`
  - Patch 2: SSDT- BKeyQ14Q15- TP- `LPCB`

  ** Note **: Some Thinkpad machines are `LPC` and some are` LPCB`. Pay attention to the selection of appropriate patch files.

- Lenovo Xiaoxin IKB / IKBR / IWL

  - Rename 1: `_Q12` to` XQ12` (LenovoAir- up)
  - Rename 2: `_Q11` to` XQ11` (LenovoAir- down)
  - Patch: *** SSDT- BKeyQ11Q12- LenAir ***

### How to find the location of the brightness shortcut

- In general, the brightness shortcut is `_Qxx` of` EC`. This `_Qxx` contains` Notify (***, 0x86) `and` Notify (***, 0x87) `instructions. If you can't find it or can't confirm, search ACPI to find the locations of `Notify (***, 0x86)` and `Notify (*** 0x87)`. Make your own patch file by referring to the sample based on the query result and rename it correctly.

- `Notify (***, 0x86)` corresponds to the up key, and `Notify (***, 0x87)` corresponds to the down key

#### Remarks

Brightness shortcuts can use keyboard mapping to specify two keys. See "PS2 Keyboard Mapping" for details.