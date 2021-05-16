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

![image]/Volumes/Macintosh/GitHub/OpenCore HotPatching/13-Fake Ethernet and reset Ethernet BSD Name/Clear all network.jpeg
