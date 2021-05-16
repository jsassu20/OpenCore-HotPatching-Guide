# AOAC wake-up method

## Description

-The ***SSDT-DeepIdle*** patch can make the machine enter a deep idle state and extend the machine's standby time. But at the same time, it will be difficult to wake up the machine, and special methods need to be taken to wake up the machine. For the content of ***SSDT-DeepIdle***, please refer to "Power Idle Management".
-**This method** **Reset the wake-up state** through a customized patch.

## Wake-up method: power button

## Brief description of wake-up principle

-Under normal circumstances, the **power button** can wake up the machine. But sometimes, the state of the machine after being awakened is not complete. It may be: unable to **light up the screen** or unable to **update the power data**. In this case, you need to add the `_PS0` method under the `LPCB` device, and add the **reset wakeup state** method in `_PS0`. Usually, the `_WAK` method [Arg0 = 3] includes the related content of **light up the screen** and **update power data**.
-According to the ACPI specification, it is recommended to use both `_PS0` and `_PS3` methods.

## Patch example

-***SSDT-PCI0.LPCB-Wake-AOAC***

  ```Swift
  ...
  Scope (_SB.PCI0.LPCB)
  {
      If (_OSI ("Darwin"))
      {
          Method (_PS0, 0, Serialized)
          {
              \_WAK(0x03) //Restore the awake state
              //May need to customize the power data recovery method
          }
          Method (_PS3, 0, Serialized)
          {
          }
      }
  }
  ...
  ```
  

## Attached

-**Light up screen conditions**
  -`_LID` returns `One`. `_LID` is the current status of the `PNP0C0D` device
  -Execute `Notify(***.LID0, 0x80)`. `LID0` is `PNP0C0D` device name

-**How ​​to update power data**

  If the power icon cannot be updated after waking up due to changing the power state during sleep (for example, unplugging or plugging in the power during sleep), please refer to the following methods:

  -Find the name and path of the power supply device (`_HID` = `ACPI0003`), search by the power source name, and record the `Method` that contains `Notify (***power source name, 0x80)`. Add this `Method` to the `_PS0` of ***SSDT-PCI0.LPCB-Wake-AOAC***.
  -`Notify (***power source name, 0x80)` may exist in multiple `Method`, which must be confirmed by the "ACPIDebug" method. **Confirmation method**: The `Method` that responds to plugging and unplugging the power supply is what we need.
