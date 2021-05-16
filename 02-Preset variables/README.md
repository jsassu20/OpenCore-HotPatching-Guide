# Binary rename

## Description

The method described in this article is not to rename `Device` or `Method` in the usual sense, but to enable or disable a device through binary rename.

## Risk

When OC boots other systems, the ACPI binary renaming may affect other systems.

## Example

Take enabling `HPET` as an example. We want its `_STA` to return `0x0F`.

Binary rename:

Find: `00 A0 08 48 50` "Note: `00` = `{`; `A0` = `If` ......"

Replace: `00 A4 0A 0F A3` "Note: `00` = `{`; `A4 0A 0F` = `Return(0x0F)`; `A3` = `Noop`, used to fill in the number of bytes"

-Original code

  ```Swift
    Method (_STA, 0, NotSerialized)
    {
        If (HPTE)
        {
            Return (0x0F)
        }
        Return (Zero)
    }
  ```

-Renamed code

  ```Swift
    Method (_STA, 0, NotSerialized)
    {
          Return (0x0F)
          Noop
          TE** ()
          Return (Zero)
    }
  ```

  **Explanation**: There was an obvious error after the name change, but this kind of error will not cause harm. First of all, the content after `Return (0x0F)` will not be executed. Secondly, the error is located in `{}` and will not affect other content.

  In reality, we should try our best to ensure the completeness of the grammar after the name change. Below is the complete `Find`, `Replace` data.
  
  Find: `00 A0 08 48 50 54 45 A4 0A 0F A4 00`
  
  Replace: `00 A4 0A 0F A3 A3 A3 A3 A3 A3 A3 A3`
  
  Complete code after `Replace`
  
  ```Swift
    Method (_STA, 0, NotSerialized)
    {
        Return (0x0F)
        Noop
        Noop
        Noop
        Noop
        Noop
        Noop
        Noop
        Noop
    }
  ```

## Claim

-***ACPI*** original file

  The `Find` binary file must be the original ***ACPI*** file and cannot be modified or saved by any software, which means it must be the original binary file provided by the machine.

-`Find` uniqueness and correctness

   The number of `Find` is only one, **unless** our intention is to perform the same operation of `Find` and `Replace` in multiple places.

   **Special attention**: Any rewriting of a piece of code to find and confirm binary data is very untrustworthy!

-`Replace` number of bytes

  The number of bytes in `Find`, `Replace` must be equal. For example, if `Find` is 10 bytes, then `Replace` is also 10 bytes. If `Replace` is less than 10 bytes, fill it with `A3` (no operation).

## `Find` data search method

Usually, use binary software (such as `010 Editor`) and `MaciASL.app` to open the same `ACPI` file, use binary data and text `Find` related content, observe the context, I believe you can quickly determine the `Find` data.

## `Replace` content

When "Find" is stated in the "Requirements", [Any binary data that rewrites a piece of code and finds confirmation from it is very untrustworthy! 】, but `Replace` can do this. According to the above example, we write a piece of code:

```Swift
    DefinitionBlock ("", "SSDT", 2, "hack", "111", 0)
    {
        Method (_STA, 0, NotSerialized)
        {
            Return (0x0F)
        }
    }
```

After compiling, open it with binary software and find: `XX...5F 53 54 41 00 A4 0A 0F`, where `A4 0A 0F` is `Return (0x0F)`.

Note: The content of `Replace` must comply with ACPI specifications and ASL language requirements.

## Precautions

-Updating the BIOS may cause the name change to become invalid. The greater the number of `Find` & `Replace` bytes, the greater the possibility of failure.

### Attachment: TP-W530 prohibits BAT1

Find: `00 A0 4F 04 5C 48 38 44 52`

Replace: `00 A4 00 A3 A3 A3 A3 A3 A3`

-Original code

  ```Swift
    Method (_STA, 0, NotSerialized)
    {
          If (\H8DR)
          {
              If (HB1A)
              {
              ...
    }
  ```

-Renamed code

  ```Swift
    Method (_STA, 0, NotSerialized)
    {
          Return (Zero)
          Noop
          Noop
          Noop
          Noop
          Noop
          Noop
          If (HB1A)
          ...
    }
  ```

# Preset variable method

## Description

-The so-called **preset variable method** is to pre-assign some ACPI variables (`Name` type and `FieldUnitObj` type) to achieve the purpose of initialization. [Although these variables have been assigned at the time of definition, they have not been changed before the `Method` calls them. 】
-Correcting these variables in `Scope (\)` through a third-party patch file can achieve our expected patch effect.

## Risk

-The modified `variable` may exist in multiple places. After modifying it, while achieving our expected results, it may affect other components.
-The modified `variable` may come from hardware information and can only be read but not written. In this case, **Binary Rename** and **SSDT Patch** need to be completed together. It should be noted that when OC boots other systems, it may not be possible to restore the renamed `variable`. See **Example 4**.

### Example 1

A device _STA original text:

```Swift
Method (_STA, 0, NotSerialized)
{
    ECTP (Zero)
    If ((SDS1 == 0x07))
    {
        Return (0x0F)
    }
    Return (Zero)
}
```

For some reason, we need to disable this device. In order to achieve this, `_STA` should return `Zero`. It can be seen from the original text that as long as `SDS1` is not equal to `0x07`. Use the **preset variable method** as follows:

```Swift
Scope (\)
{
    External (SDS1, FieldUnitObj)
    If (_OSI ("Darwin"))
    {
        SDS1 = 0
    }
}
```

### Example 2

The official patch ***SSDT-AWAC*** is used for some 300+ series machines to force enable RTC and disable AWAC at the same time.

Note: You can also use ***SSDT-RTC0*** to enable RTC, see "Counterfeit Equipment".

original:

```Swift
Device (RTC)
{
    ...
    Method (_STA, 0, NotSerialized)
    {
            If ((STAS == One))
            {
                Return (0x0F)
            }
            Else
            {
                Return (Zero)
            }
    }
    ...
}
Device (AWAC)
{
    ...
    Method (_STA, 0, NotSerialized)
    {
            If ((STAS == Zero))
            {
                Return (0x0F)
            }
            Else
            {
                Return (Zero)
            }
    }
    ...
}
```

It can be seen from the original text that as long as `STAS`=`1`, RTC can be enabled and `AWAC` can be disabled at the same time. Use the **preset variable method** as follows:

-Official patch ***SSDT-AWAC***

  ```Swift
  External (STAS, IntObj)
  Scope (_SB)
  {
      Method (_INI, 0, NotSerialized) /* _INI: Initialize */
      {
          If (_OSI ("Darwin"))
          {
              STAS = One
          }
      }
  }
  ```

  Note: The official patch introduces the path `_SB._INI`, you should make sure that DSDT and other patches do not exist `_SB._INI` when using it.

-Improved patch ***SSDT-RTC_Y-AWAC_N***

  ```Swift
  External (STAS, IntObj)
  Scope (\)
  {
      If (_OSI ("Darwin"))
      {
          STAS = One
      }
  }
  ```

### Example 3

When using the I2C patch, you may need to enable `GPIO`. See ***SSDT-OCGPI0-GPEN*** in "OCI2C-GPIO Patch".

Some original text:

```Swift
Method (_STA, 0, NotSerialized)
{
    If ((GPEN == Zero))
    {
        Return (Zero)
    }
    Return (0x0F)
}
```

It can be seen from the original text that as long as `GPEN` is not equal to `0`, `GPIO` can be enabled. Use the **preset variable method** as follows:

```Swift
External(GPEN, FieldUnitObj)
Scope (\)
{
    If (_OSI ("Darwin"))
    {
        GPEN = 1
    }
}
```

### Example 4

When the `variable` is a read-only type, the solution is as follows:

-Rename the original `variable`
-Redefine a `variable` with the same name in the patch file

Such as: a certain original text:

```Swift
OperationRegion (PNVA, SystemMemory, PNVB, PNVL)
Field (PNVA, AnyAcc, Lock, Preserve)
{
    ...
    IM01, 8,
    ...
}
...
If ((IM01 == 0x02)){
...
}
```

The actual situation is that `IM01` is not equal to 0x02, and the content of {...} cannot be executed. In order to correct the error, use **Binary Rename** and **SSDT Patch**:

**Rename**: `IM01` rename `XM01`

```text
Find: 49 4D 30 31 08
Replace: 58 4D 30 31 08
```

**patch**:

```Swift
Name (IM01, 0x02)
If (_OSI ("Darwin"))
{
...
}
Else
{
  IM01 = XM01 /* Same as the path of the original ACPI variable */
}
```
### Example 5

Use the device's original `_STA` method (Method) to be referred to as `IntObj` and assign to it to change the enable bit of the device state.

An original text: an example that can use this method to operate

```Swift
Method (_STA, 0, NotSerialized)
{
If ((XXXX == Zero))
{
    Return (Zero)
}
Return (0x0F)
}

Method (_STA, 0, NotSerialized)
{
Return (0x0F)
}

Name (_STA, 0x0F)

```
It can be seen that the above-exemplified `_STA` method only contains the enable bit that returns the device state and the enable bit that is returned based on the conditional judgment. If you don’t want to use the preset variables for renaming and changing conditions, you can use the custom SSDT You can directly reference the `_STA` method as `IntObj`

Examples of operations to disable a device:
```Swift

External (_SB_.PCI0.XXXX._STA, IntObj)

\_SB.PCI0.XXXX._STA = Zero

```
For the specific setting of the enable bit of the `_STA` method, please refer to **ASL Language Basics**

The reason why this method is effective in practical applications is mainly because in the ACPI specification the priority of the `_STA` method in the operating system OSPM module for device state evaluation and initialization is higher than the return of `_INI _ADR _HID` and `_STA` The value itself is also an integer `Integer`

An original text: an example of an operation where this method cannot be used

```Swift
Method (_STA, 0, NotSerialized)
{
ECTP (Zero)
If (XXXX == One)
{
    Return (0x0F)
{

Return (Zero)
}

Method (_STA, 0, NotSerialized)
{
^^^GFX0.CLKF = 0x03
Return (Zero)
}
```
From the above examples, it can be seen that the original `_STA` method not only sets the conditional device status enable bit, but also includes other operations `method call ECTP (Zero)` and `assignment operations ^^^GFX0.CLKF = 0x03` ,
Using this method will cause other references and operations in the original `_STA` method to fail and cause errors (non-ACPI Error)


**Risk**: The `XM01` may not be restored when the OC boots other systems.
