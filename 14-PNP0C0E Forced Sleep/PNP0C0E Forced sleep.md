# PNP0C0E Forced sleep

## `PNP0C0E` Sleep mode

- ACPI specification

  `PNP0C0E` — Sleep Button Device. For details on `PNP0C0E`, please refer to the ACPI specification.

- `PNP0C0E` sleep conditions

  Execute `Notify (***.SLPB, 0x80)`. `SLPB` is the` PNP0C0E` device name.

## Problem Description

Some machines provide sleep buttons (small moon buttons), such as: FN + F4 for ThinkPad and FN + Insert for Dell. When this button is pressed, the system executes `PNP0C0E` sleep. However, ACPI incorrectly passed shutdown parameters instead of sleep parameters to the system, causing sleep failure and system crashes. Even if you can sleep, you cannot wake up normally, and the system working state is also destroyed. To use this sleep button, you need to intercept this parameter and correct it.

## solution

Add the variable `FNOK = 0`. When pressing the sleep button, make` FNOK = 1`. In the sleep and wake methods (`_PTS` and` _WAK`) check if `FNOK` is` 1`, if `1`, force` Arg0 = 3` (`Arg0 = 5` if there is no intervention). After normal wake up, `FNOK = 0` will be restored. A complete sleep and wake process is over.

### Rename and patch combination (example: Dell Latitude 5480 and ThinkPad X1C5th)

- Dell Latitude 5480

  Key rename: `BTNV` to` XTNV` (Dell- FnInsert)

  Patch combination:

  - ***SSDT-PTSWAK***: Comprehensive patch. Built-in variable `FNOK = 0`, and make corresponding correction according to the change of` FNOK`. See "PTSWAK Comprehensive Patch and Extension Patch".
  
  - ***SSDT-FnInsert_BTNV-dell***: Sleep button patch. Execute `PNP0C0E` sleep order` FNOK = 1`.

- ThinkPad X1C5th

  Key rename: `_Q13 to XQ13` (TP- Fn + F4)

  Patch combination:

  - ***SSDT-PTSWAK***: Same as above.
  
  - ***SSDT-FnF4_Q13-X1C5th***: Same as above.
  
  **Note 1**: The sleep button of X1C5th is FN + 4, and some sleep buttons of TP are FN + F4.
  
  **Note 2**: The TP machine `LPC` controller name may be` LPC` or `LPCB`.

### Other machines implement `PNP0C0E` sleep

- Use *** SSDT- PTSWAK ***.

- Find the position of the sleep button.

  Under normal circumstances, the sleep button is `_Qxx` of` EC`. This `_Qxx` contains the` Notify (***. SLPB, 0x80) `instruction. If you can't find it or can't confirm, DSDT full- text search `Notify (***. SLPB, 0x80)`, find its position, and gradually find the final position.

  Note: SLPB is the `PNP0C0E` device name.

- Refer to the example to make a sleep button patch and rename it if necessary.

### `PNP0C0E` Sleep characteristics

- The sleep process is slightly faster.
- The sleep process cannot be terminated.
