# PCI device ASPM

## Description

-ASPM stands for Active State Power Management, which is a power link management solution supported by the system. Under ASPM management, try to enter the power saving mode when the PCI device is idle.
-Several working modes of ASPM
  -L0—Normal mode.
  -L0s—standby mode. The L0s mode can quickly enter or exit the idle state. After entering the idle state, the device is placed in a lower power consumption.
  -L1—Low power consumption standby mode. Compared with L0s, L1 will further reduce power consumption. But the time to enter or exit the idle state is longer than L0s.
  -L2—Auxiliary power mode. slightly.
-For machines with `AOAC` technology, try to change the ASPM mode of PCI devices such as `wireless network card` and `SSD` to reduce machine power consumption.
-Changing the ASPM mode of PCI devices may solve the problem that some third-party devices cannot be driven correctly during system boot. For example: SD card of RTS525A model may not be recognized in `L0s` mode (default mode). After changing it to `L1`, the recognition is correct.

## Set ASPM working mode

### `Properties` injection method [**Preferred use**]

-Inject `pci-aspm-default` separately from PCI **parent device** and its **child device**

  -**Parent Device**
    -L0s/L1 mode: `pci-aspm-default` = `03000000` [data]
    -L1 mode: `pci-aspm-default` = `02000000` [data]
    -Forbid ASPM: `pci-aspm-default` = `00000000` [data]
  -**Sub-device**
    -L0s/L1 mode: `pci-aspm-default` = `03010000` [data]
    -L1 mode: `pci-aspm-default` = `02010000` [data]
    -Forbid ASPM: `pci-aspm-default` = `00000000` [data]

-Example

  The default ASPM of Xiaoxin PRO13 wireless network card is L0s/L1, and the device path is: `PciRoot(0x0)/Pci(0x1C,0x0)/Pci(0x0,0x0)`, referring to the above method, by injecting `pci-aspm-default `Change ASPM to L1. as follows:
  
  ```text
  PciRoot(0x0)/Pci(0x1C,0x0)
  pci-aspm-default = 02000000
  ......
  PciRoot(0x0)/Pci(0x1C,0x0)/Pci(0x0,0x0)
  pci-aspm-default = 02010000
  ```

#### `SSDT` Patch Method

-SSDT patch can also set ASPM working mode. For example, set the ASPM of a certain device to L1 mode, see the example for details.

-The principle of the patch is the same as that of "Prohibiting PCI Devices", please refer to it.

-Example: ***SSDT-PCI0.RPXX-ASPM***

  ```Swift
  External (_SB.PCI0.RP05, DeviceObj)
  Scope (_SB.PCI0.RP05)
  {
      OperationRegion (LLLL, PCI_Config, 0x50, 0x01)
      Field (LLLL, AnyAcc, NoLock, Preserve)
      {
          L1, 1
      }
  }
  
  Scope (\)
  {
      If (_OSI ("Darwin"))
      {
          \_SB.PCI0.RP05.L1 = Zero /* Set ASPM = L1 */
      }
  }
  ```

  **Note 1**: The path of Xiaoxin PRO13 wireless network card is `_SB.PCI0.RP05`.

  **Note 2**: When `\_SB.PCI0.RP05.L1 = 1`, ASPM = L0s/L1; when `\_SB.PCI0.RP05.L1 = 0`, ASPM = L1.

## Precautions

-The ***Hackintool.app*** tool can view the ASPM working mode of the device.
-After changing the ASPM, if an abnormal situation occurs, please restore the ASPM.
