# Counterfeit Ethernet

## Description

Some machines don't have Ethernet, imitating Ethernet to realize normal APP download function.

## Use

-Add ***SSDT-LAN*** to `OC\ACPI`.

-Add **NullEthernet.kext** to `OC\Kexts`.

-Add related lists in config.

## Attachment: Reset the Ethernet `BSD Name` to `en0`

-Open **Network** in **System Preferences**.
-Delete all networks, as shown in "Clear All Networks".
-Delete the `Resource Library\Preferences\SystemConfiguration\NetworkInterfaces.plist` file.
-Restart.
-After entering the system again, open **Network** of **System Preferences** again.
-Add **Ethernet** and other required networks in sequence, and click **Apply**.

![image]https://github.com/jsassu20/OpenCore-HotPatching-Guide/blob/d90f5876c56e020c225bc93ccd2f4da00440f8d5/13-Fake%20Ethernet%20and%20reset%20Ethernet%20BSD%20Name/Clear%20all%20network.png
