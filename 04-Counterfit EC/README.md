# SSDT-EC Phishing Patch

## Description

OpenCore requires that you do not change the EC controller name, but in order to load USB power management, you may need to impersonate another EC.

## Instructions for use

Search `PNP0C09` in DSDT to see the device name. If the name is not `EC`, this patch is used; if it is` EC`, this patch is ignored.

## Note

- If more than one `PNP0C09` is found, confirm the real and effective` PNP0C09` device.

- `LPCB` is used in the patch. If it is not` LPCB`, please modify the patch content yourself.
