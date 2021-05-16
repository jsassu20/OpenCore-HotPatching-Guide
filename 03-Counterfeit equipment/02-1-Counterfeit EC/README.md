# SSDT-EC Counterfeit Patch

## Description

OpenCore requires not to change the EC controller name, but in order to load USB power management, it may be necessary to fake another EC.

## Instructions for use

Search for `PNP0C09` in DSDT to view the name of the device it belongs to. If the name is not `EC`, use this patch; if it is `EC`, ignore this patch.

## Note

-If multiple `PNP0C09` are found, the authentic and effective `PNP0C09` device should be confirmed.
-`LPCB` is used in the patch, if it is not `LPCB`, please modify the content of the patch by yourself.
