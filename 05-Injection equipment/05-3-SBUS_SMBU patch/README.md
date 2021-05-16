# SSDT-SBUS(SMBU) Patch

## Device name

Search for `0x001F0003` (before the 6th generation) or `0x001F0004` (for the 6th generation and later) in DSDT to view the name of the device to which it belongs.

## Patch

-The device name is `SBUS`, use ***SSDT-SBUS***
-The device name is `SMBU`, use ***SSDT-SMBU***
-The device name is another name, modify the patch related content by yourself

## Remarks

TP machines are mostly `SMBU`.
