# Fake Ethernet

## Description

Some machines do not have Ethernet, and the fake APP can download the APP normally.

## use

- Add ***SSDT-LAN*** to `OC \ ACPI`.

- Add **NullEthernet.kext** to `OC \ Kexts`.

- Add related list in config.

  

## Attachment: Reset Ethernet `BSD Name` to` en0`

- Open **Network** of **System Preferences**.
- Delete all networks, as shown in "Erase All Networks".
- Delete the `Library \ Preferences \ SystemConfiguration \ NetworkInterfaces.plist` file.
- Restart.
- After entering the system again, open **Network** of **System Preferences** again.
- Add **Ethernet** and other required networks in order, click **Apply**。

