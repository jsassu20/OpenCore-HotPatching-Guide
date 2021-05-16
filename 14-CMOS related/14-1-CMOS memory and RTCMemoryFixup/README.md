# **CMOS** Memory and RTCMemoryFixup

## Description

-When **AppleRTC** and **BIOS** conflict, you can try to use **RTCMemoryFixup** to simulate **CMOS** memory to avoid the conflict.
-**RTCMemoryFixup** download link: <https://github.com/acidanthera/RTCMemoryFixup>

## **CMOS** Memory

-**CMOS** The memory stores important data such as date, time, hardware configuration information, auxiliary settings information, startup settings, and sleep information.

-Some **CMOS** memory space definitions:

  -Date, time, hardware configuration: `00-0D`
  -Sleep information storage area: `80-AB`
  -Power management: `B0-B4`
  -Other

## View COMS memory

-`EFI\OC\Tools` install ***RtcRw.efi***
-Add `items` of ***RtcRw.efi*** in config
-The boot interface enters `Shell` [Ensure ***OpenShell.efi*** has been installed], enter the directory tools, type rtcrw read XX and press Enter. Where XX is the CMOS memory address. Such as: rtcrw read 08 can view the current month. If this month is May, the result is 0x05 (BCD code).


## Simulation **CMOS** memory

-Install **RTCMemoryFixup** to `OC\Kexts` and add the driver list.

-Boot **`boot-args`** Add `rtcfx_exclude=...`

   Format: `rtcfx_exclude=offset1,offset2,start_offset-end_offset,...` Such as: `rtcfx_exclude=0D`, `rtcfx_exclude=40-AF`, `rtcfx_exclude=2A,2D,80-AB` etc.


## Precautions

-Simulating **CMOS** memory will erase the original defined functions, please **use with caution**. For example: `rtcfx_exclude=00-0D` will cause the date and time of the machine to no longer be updated during sleep.

## Appendix: **CMOS** Memory `00-3F` Definition

| Address | Description |
| ----- | ------------------------------------------- ------------------------------------ |
| `0` | seconds |
| `1` | Second alarm |
| `2` | points |
| `3` | Sub-alarm |
| `4` | Hours |
| `5` | Hourly alarm |
| `6` | week |
| `7` | Day |
| `8` | Month |
| `9` | year |
| `A` | Status Register A |
| `B` | Status register B |
| `C` | Status Register C |
| `D` | Status register D (0: battery failure; 80: battery valid) |
| `E` | Diagnostic status byte |
| `F` | Shutdown status byte (definition for power-on diagnosis) |
| `10` | Type of floppy disk drive (bit 7-4: drive A, bit 3-0: drive B 1-360KB; 2-1.2MB; 6-1.44MB; 7-720KB) |
| `11` | Reserved |
| `12` | Hard disk drive type (bit 7-4: C drive, bit 3-0: D drive) |
| `13` | Reserved |
| `14` | Device bytes (number of floppy drives, display type, coprocessor) |
| `15` | Low byte of basic memory |
| `16` | Basic memory high byte |
| `17` | Extended memory low byte |
| `18` | Extended memory high byte |
| `19` | First main hard drive type |
| `1A` | First slave hard drive type |
| `1B—1C` | Reserved |
| `1D—24` | The cylinder, head, sector, etc. of the first master hard disk |
| `25—2C` | The first slave hard disk cylinder, head, sector, etc. |
| `2D` | Reserved |
| `2E—2F` | CMOS checksum (10-2D sum of each byte) |
| `30` | Extended memory low byte |
| `31` | Extended memory high byte |
| `32` | Date century byte (19H:19th century) |
| `33` | Information Sign |
| `34—3F` | Reserved |
