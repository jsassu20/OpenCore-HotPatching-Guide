# Daliansky's OC-Little Repo Translated to English

THIS SHIT TAKES FOREVER TO TRANSLATE WITH GOOGLE SO BARE WITH ME IF I DON'T KEEP IT UP TO DATE REGULARLY.

https://github.com/daliansky/OC-little

# OpenCore 0.5+ component patch

## Description

According to the requirements and suggestions of OpenCore 0.5+, make this component patch.

## Online Manual

This repository relies on GitBook and uses GitHub Actions to build the Pages service and PDF manuals.

-[https://ocbook.tlhub.cn](https://ocbook.tlhub.cn)
-[OpenCore Component Library](https://cdn.jsdelivr.net/gh/daliansky/OC-little/docs/OpenCore Component Library.pdf)

## main content

0. **Overview**

   1. ASL Grammar Basics
   2. SSDT patch loading sequence
   3. ACPI Form
   4. ASL-AML comparison table

1. **About `AOAC`**

   1. No `S3` sleep
   2. `AOAC` disables independent display
   3. Power Idle Management
   4. `AOAC` Wake Up Patch
   5. Automatically turn off Bluetooth `WIFI` during sleep

2. **Preset Variables**

   1. OC `I2C-GPIO` patch
   2. Patch Library

3. **Counterfeit Device**

   1. Counterfeit `EC`
   2. RTC0
   3. Counterfeit ambient light sensor

4. **Operating System Patches**

5. **Injection Device**

   1. Inject X86
   2. `PNLF` injection method
   3. `SBUS(SMBU)` Patch

6. **Add missing parts**

7. **PS2 keyboard mapping and brightness shortcut keys @OC-xlivans**

8. **Battery Patch**

   1. Thinkpad
   2. Other brands
   3. Battery information auxiliary patch
   4. Illustrative examples

9. **Disable EHCx**

10. **`PTSWAK` Comprehensive Extension Patch**

11. **`PNP0C0E` sleep correction method**

12. **`0D6D` Patch**

    1. Common `060D` patch
    2. HP's special `060D` patch

13. **Fake Ethernet and reset Ethernet `BSD Name`**

14. **`CMOS` related**

    1. `CMOS` memory and ***RTCMemoryFixup***

15. **`ACPI` customized `USB` port**

16. **Prohibit `PCI` device and set `ASPM` working mode**
    1. Prohibit `PCI` devices
    2. Set the working mode of `ASPM`

17. **ACPIDebug**

18. **Special Patches for Brand Machines**

    1. `Dell` machine special patch
    2. Xiaoxin PRO13 Special Patch
    3. Special Patches for ThinkPad Machines

19. **`I2C` special parts**

20. **`SSDT` method of shielding independent display**

**Keep parts:**

   1. Sound card `IRQ` patch
   2. `CMOS` reset patch

**Common driver loading sequence:**

   1. config-1-Lilu-SMC-WEG-ALC driver list
   2. config-2-PS2 keyboard driver list
   3. config-3-BCM wireless and bluetooth driver list
   4. config-4-I2C+PS2 driver list
   5. config-5-PS2Smart keyboard driver list
   6. config-6-Intel wireless and Bluetooth driver list

### Credits

- Special thanks to:
  -ACPI component patch made by @宪武 for **[OpenCore](https://github.com/acidanthera/OpenCorePkg)**
  -@Bat.bat, @黑果小兵, @套陆, @iStar, Forever reviewed and perfected

-Thanks:
  -@冬瓜-X1C5th
  -@OC-xlivans
  -@Air 13 IWL-GZ-Big Orange (OC perfect)
  -@子骏oc IWL
  -@大勇-小新air13-OC- Pitching Xiaobai
  -......

-**Thanks for [Acidanthera](https://github.com/acidanthera) maintaining [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)**
