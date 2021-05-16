# PS2 keyboard mapping

## Description

- Keyboard mapping can achieve the effect of one key after another is pressed. For example, you can specify that after pressing `A / a`, the printout is` Z / z`. For another example, specify `F2` to implement the original` F10` function.
- If you assign 2 keys to `F14`,` F15`, you can realize the brightness adjustment function, which has the same effect as the traditional brightness shortcut key patch.
- Not all keys can be mapped. Only keys that can capture the `PS2 scan code` under the MAC system can be mapped.

## Claim

- Use VoodooPS2Controller.kext and its sub- drivers.
- Clear the contents of the previous key mapping by other methods.

### PS2 scan code and ABD scan code

One key will generate 2 scan codes, which are ** PS2 scan code ** and ** ABD scan code **. For example, the PS2 scan code of the `Z / z` key is` 2c`, and the ABD scan code is `6`. Because of different scanning codes, there are two mapping methods, which are:

- `PS2 scan code —> PS2 scan code`
- `PS2 scan code —> ADB scan code`

### Get keyboard scan code

- Consult the header file `ApplePS2ToADBMap.h`, which lists the scan codes for most keys.

- Get keyboard scan code on console (choose one)

  - Terminal installation `ioio`

```bash
ioio - s ApplePS2Keyboard LogScanCodes 1
```

  - ** Modify ** `VoodooPS2Keyboard.kext \ info \ IOKitPersonalities \ Platform Profile \ Default \` **`LogScanCodes`**` = `**` 1`**

  Open the console and search for `ApplePS2Keyboard`. Press the keys, such as `A / a`,` Z / z`.

```log
11: 58: 51.255023 +0800 kernel ApplePS2Keyboard: sending key 1e = 0 down
11: 58: 58.636955 +0800 kernel ApplePS2Keyboard: sending key 2c = 6 down
```

  among them:

  The `1e` in` 1e = 0` in the first line is the PS2 scan code for the `A / a` key, and` 0` is the ADB scan code.

  The 2c in the second line of 2c = 6 is the PS2 scan code for the Z / z key, and 6 is the ADB scan code.

### Mapping method

Keyboard mapping can be achieved by modifying the `VoodooPS2Keyboard.kext \ info.plist` file and adding third- party patch files. The method of using a third- party patch file is recommended.

Example: *** SSDT- RMCF- PS2Map- AtoZ ***. `A / a` maps` Z / z`.

- `A / a` PS2 scan code:` 1e`
- `Z / z` PS2 scan code:` 2c`
- `Z / z` ADB scan code:` 06`

Choose one of the following two mapping methods

#### PS2 scan code —> PS2 scan code

```Swift
...
"Custom PS2 Map", Package ()
 {
     Package () {},
     "1e = 2c",
 },
...
```

#### PS2 scan code —> ADB scan code

```Swift
...
"Custom ADB Map", Package ()
 {
     Package () {},
     "1e = 06",
 }
...
```

### Precautions

- The keyboard path in the example is `\ _SB.PCI0.LPCB.PS2K`. When using it, make sure that the path is the same as the ACPI keyboard path. The keyboard path for most Thinkpad machines is `\ _SB.PCI0.LPC.KBD` or` \ _SB.PCI0.LPCB.KBD`.

- The variable `RMCF` is used in the patch. If` RMCF` is also used in other **keyboard patches**, it must be combined and used. See ***SSDT- RMCF- PS2Map- dell***. `Note`: *** SSDT- RMCF- MouseAsTrackpad *** is used to forcibly enable the touchpad setting option.
