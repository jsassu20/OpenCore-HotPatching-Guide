# ThinkPad Battery Patching

## Description

- Please read the "Accessories" "ThinkPad Battery System".
- Confirm the main battery path is `\ _SB.PCI0.` **`LPC`**`.EC.BAT0` or` \ _SB.PCI0.`**`LPCB`**`.EC.BAT0`, not both The contents of this chapter are for reference only.
- Possible rename
  - TP battery basically changed its name
  - TP battery `Mutex` reset to` 0`
  - TP prohibits `BAT1` rename
  - TP dual physical battery `Notify` renamed- ` LPC`
  - TP dual physical battery `Notify` changed its name- ` LPCB`
- Possible patches
  - ***SSDT-OCBAT0-TP***
  - ***SSDT-OCBATC-TP-`LPC`***
  - ***SSDT-OCBATC-TP-`LPCB`***

## Rename and Patch

- Single battery system

  - Rename:
    - TP battery basically changed its name
    - TP battery `Mutex` reset to` 0`
  - Patch
    - *** SSDT- OCBAT0- TP ***
- One battery for dual battery system

  - Renamed
    - TP battery basically changed its name
    - TP battery `Mutex` reset to` 0`
    - TP prohibits `BAT1` rename
  - Patch
    - *** SSDT- OCBAT0- TP ***
- Two physical batteries for dual battery system

  - Renamed
    - TP battery basically changed its name
    - TP battery `Mutex` reset to` 0`
    - TP dual physical battery `Notify` rename- `LPC` or TP dual physical battery` Notify` rename- `LPCB`
  - Patch

    - ***SSDT-OCBAT0-TP***
    - ***SSDT-OCBATC-TP-`LPC`*** or ***SSDT-OCBATC-TP-`LPCB`***

## Precautions

- *** SSDT- OCBAT0- TP *** is the main battery patch. When selecting, select the corresponding patch according to the machine model.

- Pay attention to the differences between `LPC` and` LPCB` when choosing renames and patches.

- `TP forbids BAT1 renaming` sample does not verify all Thinkpad machines, please try.

- Do you need to change the TP battery Mutex to 0 and rename it? Try it yourself.

### Accessories: ThinkPad battery system

#### Overview

Thinkpad battery systems are divided into single battery systems and dual battery systems.

- Dual battery system means that the machine is equipped with two batteries. Or, although the machine is only equipped with one battery, the physical interface of the second battery and the corresponding ACPI are provided. The second battery is optional and can be installed later. Machines with dual battery systems may have one battery or two batteries.
- The single battery system is an ACPI with one battery and only one battery.

- For example, the battery structure of T470, T470s belongs to dual battery system, and the battery structure of T470P belongs to single battery system. For another example, the T430 series is a dual battery system. The machine itself has only one battery, but a second battery can be installed through the optical drive.

#### Single and dual battery system judgment

- Dual battery system: Both `BAT0` and` BAT1` exist in ACPI.
- Single battery system: only `BAT0` in ACPI, no` BAT1`.
