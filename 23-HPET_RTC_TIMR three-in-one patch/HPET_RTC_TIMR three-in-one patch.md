## HPET_RTC_TIMR three-in-one patch

#### Description

-The sound card of the early machine requires parts **HPET** [PNP0103] Provide interrupt numbers 0 and 8, otherwise the sound card will not work normally. In fact, the **HPET** of almost all machines does not provide any interrupt number. Normally, interrupt numbers 0 and 8 are occupied by **RTC**[PNP0B00] and **TIMR**[PNP0100] respectively.
-To solve the above problems, the **HPET**, **RTC**, **TIMR** need to be repaired simultaneously.

#### Patch Principle

-Disable the three parts **HPET**, **RTC** and **TIMR**.
-Counterfeit three parts, namely: **HPE0**, **RTC0**, **TIM0**.
-Remove and add `IRQNoFlags (){8}` of **RTC0** and `IRQNoFlags (){0}` of **TIM0** to **HPE0**.

#### Patch method

-Disable **HPET**, **RTC**, **TIMR**
  -**HPET**
  
    Usually there is `_STA` in HPET, therefore, to disable HPET, you need to use the "preset variable method". Such as:
  
    ```
        External (HPAE, IntObj)
        //External (HPTE, IntObj)
        Scope (\)
        {
            If (_OSI ("Darwin"))
            {
                HPAE =0
                //HPTE =0
            }
        }
    ```
  
    Note: The variable randomizer of `HPAE` in `_STA` may be different.
  
  -**RTC**
  
    The RTC of the early machines did not have `_STA`, press the `Method (_STA,` method to disable RTC. For example:
  
    ```
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
  
    Same as **RTC**.
  
-Patch file: ***SSDT-HPET_RTC_TIMR-fix***

  See the above **patch principle**, refer to the example.

#### Precautions

-This patch cannot be used at the same time as the following patches:
  -***SSDT-RTC_Y-AWAC_N*** of "Preset Variable Method"
  -OC official ***SSDT-AWAC***
  -"Counterfeit Device" or OC official ***SSDT-RTC0***
  -***SSDT-RTC0-NoFlags*** of "CMOS Reset Patch"
-The name of `LPCB` and the name of **three parts** should be the same as the original ACPI part name.
