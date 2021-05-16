# Sound card IRQ patch

## Description

-The sound card of the early machine requires parts **HPET** **`PNP0103`** Provide interrupt number `0` & `8`, otherwise the sound card will not work normally. In fact, the **HPET** of almost all machines does not provide any interrupt number. Normally, the interrupt numbers `0` & `8` are occupied by **RTC** **`PNP0B00`**, **TIMR** **`PNP0100`** respectively
-To solve the above problems, you need to fix **HPET**, **RTC**, **TIMR** simultaneously.

## Patch principle

-Disable the three parts **HPET**, **RTC**, **TIMR**.
-Counterfeit three parts, namely: **HPE0**, **RTC0**, **TIM0**.
-Remove and add `IRQNoFlags (){8}` of **RTC0** and `IRQNoFlags (){0}` of **TIM0** to **HPE0**.

## Patch method

-Disable **HPET**, **RTC**, **TIMR**
  -**HPET**
  
    Usually there is `_STA` in HPET, therefore, to disable HPET, you need to use the "preset variable method". Such as:
  
    ```Swift
    External (HPAE, IntObj) /* or External (HPTE, IntObj) */
    Scope (\)
    {
        If (_OSI ("Darwin"))
        {
            HPAE =0 /* or HPTE =0 */
        }
    }
    ```
  
    Note: The variable randomizer of `HPAE` in `_STA` may be different.
  
  -**RTC**
  
    The RTC of the early machines did not have `_STA`, press the `Method (_STA,` method to disable RTC. For example:
  
    ```Swift
    Method (_STA, 0, NotSerialized)
    {
        If (_OSI ("Darwin"))
        {
            Return (0)
        }
        Else
        {
            Return (0x0F)
        }
    }
    ```
  
  -**TIMR**
  
    Same as **RTC**
  
-Patch file: ***SSDT-HPET_RTC_TIMR-fix***

  See the above **patch principle**, refer to the example.
  
  **Top Charge**
  
  Although the early platform (Intel 3 Ivy Bridge generation mobile terminal is the most common), there is a common `IRQ` problem, which causes the onboard sound card to fail to drive. The performance is that `AppleHDA.kext` cannot be loaded, and only `AppleHDAController.kext` is loaded. Some machines on the platform still have this problem. Since HPET is an obsolete device from the Intel 6th generation platform, it is reserved for compatibility with earlier versions of the system. If you use the 6th generation or higher platform and the system version Windows 8.1 + HPET (High Precision Event in Device Manager) Timer) is already in the unloaded drive state
  In macOS 10.12 + version, if this problem occurs on the 6th generation + hardware platform, you can directly block HPET to solve the problem. Refer to the specific settings of the HPET `_STA` method in the original DSDT.
    
## Precautions

-This patch cannot be used at the same time as the following patches:
  -***SSDT-RTC_Y-AWAC_N*** of "Binary Rename and Preset Variables"
  -OC official ***SSDT-AWAC***
  -"Counterfeit Device" or OC official ***SSDT-RTC0***
  -***SSDT-RTC0-NoFlags*** of "CMOS Reset Patch"
-The name of `LPCB`, the name of **three parts** and the name of `IPIC` should be the same as the name of the original `ACPI` part.
-If the three-in-one patch cannot be solved, try ***SSDT-IPIC*** under the premise of using the three-in-one patch. Follow the above method to disable ***HPET***, ***RTC*** and ***TIMR*** to disable the ***IPIC*** device, and then fake a ***IPI0*** device , The device content is the ***IPIC*** or ***PIC*** device content in the original `DSDT`, and finally delete `IRQNoFlags{2}`, refer to the example.
