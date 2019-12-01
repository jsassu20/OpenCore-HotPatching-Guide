# SSDT- SBUS (SMBU) patch

## Device name

Search DSDT for `0x001F0003` (before 6th generation) or` 0x001F0004` (for 6th generation and later) to see the device name.

## Patch

- The device name is `SBUS`, use  ***SSDT- SBUS***.
- The device name is `SMBU`, use  ***SSDT- SMBU***.
- The device name is another name. Modify the patch content yourself.

## Notes

TP machines are mostly `SMBU`.
