# OpenCore 0.5+ component patch

> August 26 update

## Description

According to the requirements and suggestions of OpenCore 0.5, make this part patch

## main content

-ACPI binary rename
-Preset variable method
-Inject X86
-Counterfeit `EC`
-OC-`PNLF` injection method
-`SBU(SMBU)` patch
-PS2 keyboard mapping @OC-xlivans
-Emergency solution `PS2N` for keyboard unable to input
-Add missing parts
-OC `I2C-TPXX` patch method
-OC battery (TP)
-Disable EHCx
-`PTSWAK` comprehensive patch and extended patch
-`PNP0C0E` forced sleep
-`0D6D` patch
-For ease of use, some driver lists have been organized, **for reference only**:

  -config-Lilu-SMC-WEG-ALC driver list
  -config-PS2 keyboard driver list
  -config-I2C forbidden native driver list
  -config-I2C driver list
  -config-BCM wireless bluetooth driver list

## Reserved items

-Operating system rename patch
-Brightness shortcut key patch

## QQ exchange group

538643249 `OpenCore` technical exchange group [click to enter](//shang.qq.com/wpa/qunwpa?idkey=39aee9ea4d248cd6fe1e4e57e79551c5cbf85af615d55be08a364fd7c213c906)

### Credits

- Special thanks to:
  -ACPI component patch made by @宪武 for **[OpenCore](https://github.com/acidanthera/OpenCorePkg)**
  -@[Bat.bat](https://github.com/williambj1), @[黑果小兵](github.com/daliansky) Complete review

-Thank you: @冬瓜-X1C5th, @OC-xlivans, @Air 13 IWL-GZ-Big Orange (OC perfect), @子骏oc IWL, @大勇-小新air13-OC-打水小白, etc.

-Thanks to [Acidanthera](https://github.com/acidanthera) for providing [AppleSupportPkg](https://github.com/acidanthera/AppleSupportPkg), ~~[AptioFixPkg](https://github.com/ acidanthera/AptioFixPkg)~~(Achieved), [MacInfoPkg](https://github.com/acidanthera/MacInfoPkg), [OCSupportPkg](https://github.com/acidanthera/OCSupportPkg) and [OpenCorePkg](https: //github.com/acidanthera/OpenCorePkg).
