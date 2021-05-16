# GPI0 patch

## Description

-If you want to enable GPI0, its `_STA` must be `Return (0x0F)`.

-The sample is for reference only. When using it, make sure that the `_STA` of the GPI0 device has `GPEN` or `GPHD`. See "Binary Rename and Preset Variables" for details.
