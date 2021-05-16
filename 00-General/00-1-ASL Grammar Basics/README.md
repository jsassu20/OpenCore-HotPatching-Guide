# ASL Language Basics

> This tutorial is reproduced from the Vision Forum, published on 2011-11-21 11:16:20, and the author is suhetao.
>
> Re-formatted as Markdown by Bat.bat(williambj1) on 2020-2-14, and added and revised some of the content.
>
> <http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=944566&archive=2&extra=page%3D1&page=1>

## Written in front of words

I am not a motherboard BIOS developer. The following understanding of ASL is only derived from ~~<http://www.acpi.info/>~~ [Note: It is invalid, the document has been transferred to <https://uefi. The ACPI Specification document on org>]. Therefore, it is inevitable that there will be many wrong understandings and wrong views. I hope everyone can understand and correct them.

## Brief description

First of all, I have to say what DSDT (Differentiated System Description Table Fields) and SSDT (Secondary System Description Table Fields) are DSDT and SSDT? In fact, they all belong to one of the ACPI tables, and ACPI is the abbreviation of Advanced Configuration & Power Interface. It can be understood from the text that ACPI is a series of interfaces. This interface contains many tables, so DSDT and SSDT is not only one of the tables but also some interfaces. So it is not difficult to imagine that the main function of ACPI is to provide some services of the operating system and provide some information to the operating system. DSDT and SSDT are naturally no exception. A feature of ACPI is a proprietary language to compile those tables of ACPI. It is ASL (ACPI Source Language), which is the protagonist of this article. After ASL is compiled by a compiler, it becomes AML (ACPI Machine Language), which is then executed by the operating system. Since ASL is a language, it has its rules.

## ASL Guidelines

1. The variable name cannot exceed 4 characters and cannot start with a number. Think about the DSDT code you have seen, it will definitely not exceed.

1. Scope forms a scope, and the concept is similar to the set `{}` in mathematics. There is one and only one root scope, so DSDT all end with

   ```swift
   DefinitionBlock ("xxxx", "DSDT", 0x02, "xxxx", "xxxx", xxxx)
   {
   ```

   Start with

   ```swift
   }
   ```

   the end. This is the root scope.

   The `xxxx` parameters in turn indicate the output of `file name`, `OEMID`, `table ID`, and `OEM version`. The third parameter is specified according to the second parameter, as shown above. If it is **`DSDT`**, it must be `0x02`, and other parameters can be modified freely.

1. The functions and variables beginning with the character `_` are reserved by the system. This is why the ASL obtained after decompiling some AML appears `_T_X`, and a warning appears when recompiling.

1. `Method` defines functions. Functions can be defined under `Device` or under `Scope`, but they cannot be separated from `Scope` to define separate functions, so **no** the following situations will occur.

   ```swift
   Method (xxxx, 0, NotSerialized)
   {
       ...
   }
   DefinitionBlock ("xxxx", "DSDT", 0x02, "xxxx", "xxxx", xxxx)
   {
       ...
   }
   ```

1. Under the root scope `\`, there are five scopes: `\_GPE`, `\_PR`, `\_SB`, `\_SI`, `\_TZ`.

   -`\_GPE`--- ACPI event handling
   -`\_PR` --- Processor
   -`\_SB` --- All devices and buses
   -`\_SI` --- System indicator
   -`\_TZ` --- Hot zone, used to read certain temperatures

   > **Things with different attributes are placed under the corresponding scope. E.g:**

   -The device `Device (PCI0)` is placed in `Scope (\_SB)`

     ```swift
     Scope (\_SB)
     {
         Device (PCI0)
         {
             ...
         }
         ...
     }
     ```

   -CPU related information is placed in `Scope (_PR)`

     > Different CPUs have different domains. Common ones are `_PR`, `_SB`, `SCK0`, etc.

     ```swift
     Scope (_PR)
     {
         Processor (CPU0, 0x00, 0x00000410, 0x06)
         {
             ...
         }
         ...
     }
     ```

   -`Scope (_GPE)` puts related event processing

      ```swift
      Scope (_GPE)
      {
          Method (_L0D, 0, NotSerialized)
          {
              ...
          }
          ...
      }
      ```

      Does it look like a function at first glance? Of course functions can also be placed here. But please note that the function name **`_`** starts with the function reserved by the system.

1. `Device (xxxx)` can also be regarded as a scope, which contains various descriptions of the device, such as `_ADR`, `_CID`, `_UID`, `_DSM`, `_STA`, etc. Description and status control

1. The symbol `\` refers to the root scope, `^` refers to the superior scope, and similarly, `^^` is the superior scope of `^`

1. The symbol `_` itself has no meaning and is only used to fill in the position in ASL. For example, the `_` of `_OSI` is used to fill the four characters required for the name of `Method`

1. For better understanding of ACPI, the specification later used the new ASL grammar ASL 2.0 (also called ASL+), the new grammar introduced the equivalent of C language `+-*/=`, `<<`, `>>` And logical judgment `==`, `!=` etc.

1. The function can pass up to 7 parameters, which are represented by `Arg0`~`Arg6` in the function, and cannot be customized.

1. The function can use up to 8 local variables, using `Local0`~`Local7`, no definition is needed, but it needs to be initialized before it can be used, that is, there must be an assignment operation.

## ASL commonly used data types

| ASL | Chinese |
| :-------: | :------: |
| `Integer` | Integer |
| `String` | String |
| `Event` | Event |
| `Buffer` | Array |
| `Package` | Object Collection |

## ASL define variables

-Define integer

  ```swift
  Name (TEST, 0)
  ```

-Define string
  
  ```swift
  Name (MSTR,"ASL")
  ```

-Define Package

  ```swift
  Name (_PRW, Package (0x02)
  {
      0x0D,
      0x03
  })
  ```

-Define Buffer Field

  > There are 6 types of Buffer Fields, namely

| Create statement | Size |
| :--------------: | :------: |
| CreateBitField | 1-Bit |
| CreateByteField | 8-Bit |
| CreateWordField | 16-Bit |
| CreateDWordField | 32-Bit |
| CreateQWordField | 64-Bit |
| CreateField | Any size |

  ```swift
  CreateBitField (AAAA, Zero, CCCC)
  CreateByteField (DDDD, 0x01, EEEE)
  CreateWordField (FFFF, 0x05, GGGG)
  CreateDWordField (HHHH, 0x06, IIII)
  CreateQWordField (JJJJ, 0x14, KKKK)
  CreateField (LLLL, Local0, 0x38, MMMM)
  ```

It can be found that there is no need to explicitly declare its type when defining a variable

## ASL assignment method

There is one and only one assignment method

```swift
Store (a,b) /* Traditional ASL */
b = a /* ASL+ */
```

example:

```swift
Store (0, Local0)
Local0 = 0

Store (Local0, Local1)
Local1 = Local0
```

## ASL arithmetic function

| ASL+ | Traditional ASL | Chinese meaning | Examples |
| :----: | :--------: | :--------: | :------------------- ---------------------------------------- |
| + | Add | Integer addition | `Local0 = 1 + 2`<br/>`Add (1, 2, Local0)` |
|-| Subtract | Integer Subtraction | `Local0 = 2-1`<br/>`Subtract (2, 1, Local0)` |
| * | Multiply | Multiplying integers | `Local0 = 1 * 2`<br/>`Multiply (1, 2, Local0)` |
| / | Divide | Integer division | `Local0 = 10 / 9`<br/>`Divide (10, 9, Local1(remainder), Local0(result))` |
|% | Mod | Integer remainder | `Local0 = 10% 9`<br/>`Mod (10, 9, Local0)` |
| << | ShiftLeft | Shift Left | `Local0 = 1 << 20`<br/>`ShiftLeft (1, 20, Local0)` |
| >> | ShiftRight | Shift Right | `Local0 = 0x10000 >> 4`<br/>`ShiftRight (0x10000, 4, Local0)` |
| - | Decrement | Integer decrement by 1 | `Local0--`<br/>`Decrement (Local0)` |
| ++ | Increment | Increment by integer 1 | `Local0++`<br/>`Increment (Local0)` |
| & | And | Integer in | `Local0 = 0x11 & 0x22`<br/>`And (0x11, 0x22, Local0)` |
| &#124; | Or | or | `Local0 = 0x01`&#124;`0x02`<br/>`Or (0x01, 0x02, Local0)` |
| ~ | Not | Invert | `Local0 = ~(0x00)`<br/>`Not (0x00,Local0)` |
| None | Nor | XOR | `Nor (0x11, 0x22, Local0)` |

Wait, please refer to ACPI Specification for details

## ASL logic operation

| ASL+ | Traditional ASL | Chinese meaning | Examples |
| :----: | :-----------: | :----------: | :-------------- --------------------------------------------- |
| && | LAnd | Logical AND | `If (BOL1 && BOL2)`<br/>`If (LAnd(BOL1, BOL2))` |
|! | LNot | Logical inverse | `Local0 = !0`<br/>`Store (LNot(0), Local0)` |
| &#124; | LOr | Logical OR | `Local0 = (0`&#124;`1)`<br/>`Store (LOR(0, 1), Local0)` |
| <| LLess | Logical less than | `Local0 = (1 <2)`<br/>`Store (LLess(1, 2), Local0)` |
| <= | LLessEqual | Logical less than or equal to | `Local0 = (1 <= 2)`<br/>`Store (LLessEqual(1, 2), Local0)` |
|> | LGreater | Logical greater than | `Local0 = (1> 2)`<br/>`Store (LGreater(1, 2), Local0)` |
| >= | LGreaterEqual | Logical greater than or equal to | `Local0 = (1 >= 2)`<br/>`Store (LGreaterEqual(1, 2), Local0)` |
| == | LEqual | logical equality | `Local0 = (Local0 == Local1)`<br/>`If (LEqual(Local0, Local1))` |
| != | LNotEqual | Logical not equal to | `Local0 = (0 != 1)`<br/>`Store (LNotEqual(0, 1), Local0)` |

It is not difficult to find that logical operations will only have two results, `0` or `1`

## ASL function definition

1. Define the function

   ```swift
   Method (TEST)
   {
       ...
   }
   ```

1. Define a function with two input parameters and use local variables `Local0`~`Local7`

   If the number of parameters is not defined, the default is `0`

   ```swift
   Method (MADD, 2)
   {
       Local0 = Arg0
       Local1 = Arg1
       Local0 += Local1
   }
   ```

   The addition operation of two parameters is implemented, so the incoming parameters must be implicitly integer numbers.

1. Define a function with return value
  
   ```swift
   Method (MADD, 2)
   {
       Local0 = Arg0
       Local1 = Arg1
       Local0 += Local1

       Return (Local0) /* Return here */
   }
   ```

   The example is the realization of custom addition. The function implements the same addition as the system function Add

   ```swift
   Local0 = 1 + 2 /* ASL+ */
   Store (MADD (1, 2), Local0) /* Traditional ASL */
   ```

1. Define serializable functions

   If you do not define `Serialized` or `NotSerialized`, the default is `NotSerialized`

   ```swift
   Method (MADD, 2, Serialized)
   {
       Local0 = Arg0
       Local1 = Arg1
       Local0 += Local1
       Return (Local0)
   }
   ```

   This is somewhat similar to the concept of multi-thread synchronization, that is, when a function is declared as `Serialized`, only one instance can exist in the memory. Generally used to create an object in a function. Application examples:

   ```swift
   Method (TEST, Serialized)
   {
       Name (MSTR,"I will sucess")
   }
   ```

   If you declare the function `TEST` like this, then call this function in two places at the same time

   ```swift
   Device (Dev1)
   {
        TEST ()
   }
   Device (Dev2)
   {
        TEST ()
   }
   ```

   If the `TEST` of `Dev1` is executed first, the `TEST` of `Dev2` will wait for the completion of the `TEST` function of `Dev1` before executing. If declared as

   ```swift
   Method (TEST, NotSerialized)
   {
       Name (MSTR, "I will sucess")
   }
   ```

   Then when one of the `Devx` calls `TEST`, the other call attempts to create the same string `MSTR` will fail.

## ACPI predefined functions

### `_OSI` (Operating System Interfaces)

By calling the `_OSI` function, we can easily know what system the current system is running, and easily distinguish between Windows and macOS, so as to create a separate patch for a certain system.

The `_OSI` function needs to enter a parameter, which must be a string defined by the operating system for identification

| Operating System | String |
| :---------------------------------------: | :------ --------: |
| macOS | `"Darwin"` |
| Linux<br/>(including operating systems based on Linux kernel) | `"Linux"` |
| FreeBSD | `"FreeBSD"` |
| Windows | `"Windows 20XX"` |

> Different from other operating systems, each Windows version has a different string, see:
>
> <https://docs.microsoft.com/en-us/windows-hardware/drivers/acpi/winacpi-osi>

When the parameters in `_OSI` correspond to the current operating system, `_OSI` will return `1`, and the conditional statement of `If` is established

```swift
If (_OSI ("Darwin")) /* Determine whether the current system is macOS */
```

### `_STA` (Status Status)

**Note⚠️: There are two types of `_STA`, please do not confuse with `_STA` of `PowerResource`! ! ! **

The return value of the `_STA` method contains up to 5 Bits, and the meaning of each Bit is as follows

| Bit Bit | Meaning |
| :-----: | :----------------------------- |
| Bit [0] | Does the device exist |
| Bit [1] | Is the device enabled and can decode its resources |
| Bit [2] | Is the device displayed in the UI |
| Bit [3] | Is the device working normally |
| Bit [4] | Does the device have a battery |

After reading the above table, someone may ask, "The `_STA` we usually see are `0x0F`, `Zero`". What does it have to do with these bits? In fact, as long as the hexadecimal value is converted to the binary system, it will be clear at a glance. `0x0F` means that the first four bits are all `1`, which is equivalent to fully enabled, otherwise `Zero` is completely disabled.

So what is `0x0B` and `0x1F` sometimes encountered? The `0x0B` in **`SSDT-PNLF`** converted to binary is `1011`, which means that the device is in a half-open state and it is not allowed to decode the resources in it. `0x1F` will only appear in the battery device of the notebook. The extra bit is used to notify the battery control device (Control Method Battery Device `PNP0C0A`) that the device has a battery.

> Here is another word of `_STA` of `PowerResource`
>
> The `_STA` of `PowerResource` only has two return values, `One` and `Zero`. If you are interested, you can consult the ACPI specification, and the function will not be repeated

### `_CRS` (Current Resource Settings Current Resource Settings)

The `_CRS` function returns a `Buffer`. In the touch device, it returns the `GPIO Pin`, `APIC Pin`, etc. required by the touch device, which can control the interrupt mode of the device.

## ASL Process Control

Like common high-level programming languages, ASL also has corresponding control flow statements.

-Switch
  -Case
  -Default
  -BreakPoint
-While
  -Break
  -Continue
-If
  -Else
  -ElseIf
-Stall

### Branch control `If` and `Switch`

#### `If`

   For example, the following statement determines whether the interface of the current system is `Darwin`, if it is `OSYS = 0x2710`

   ```swift
   If (_OSI ("Darwin"))
   {
       OSYS = 0x2710
   }
   ```

#### `ElseIf`, `Else`

   In the following example, if the system structure is not `Darwin`, and if the system is not `Linux`, then `OSYS = 0x07D0`

   ```swift
   If (_OSI ("Darwin"))
   {
       OSYS = 0x2710
   }
   ElseIf (_OSI ("Linux"))
   {
       OSYS = 0x03E8
   }
   Else
   {
       OSYS = 0x07D0
   }
   ```

#### `Switch`, `Case`, `Default`, `BreakPoint`

   ```swift
   Switch (Arg2)
   {
       Case (1) /* Condition 1 */
       {
           If (Arg1 == 1)
           {
               Return (1)
           }
           BreakPoint /* If the condition is not met, exit */
       }
       Case (2) /* Condition 2 */
       {
           ...
           Return (2)
       }
       Default /* If none of them match, then */
       {
           BreakPoint
       }
   }
   ```

   `Switch` can be regarded as a series of `If...Else` collections. `BreakPoint` is equivalent to a breakpoint, meaning to exit the current `Swtich`

### Loop control

#### `While` and pause `Stall`

```swift
Local0 = 10
While (Local0 >= 0x00)
{
    Local0--
    Stall (32)
}
```

`Local0` is equal to `10`, if the logic of `Local0` is not equal to `0`, it is not true, `Local0` will decrement by `1` and pause for `32μs`, so this code delays `10 * 32 = 320 μs` .

#### `For`

The For loop in ASL is exactly the same as in `C` and `Java`

```swift
for (local0 = 0, local0 <8, local0++)
{
    ...
}
```

The above `For` loop and the following `While` are equivalent

```swift
Local0 = 0
While (Local0 <8)
{
    Local0++
}
```

## External reference `External`

| Reference type | Chinese (for reference only) | External SSDT reference | Cited |
| :------------: | :------------: | :------------------ -------------------------- | :---------------------- ------------------------------------------------ |
| UnknownObj | Unknown | `External (\_SB.EROR, UnknownObj` | (Appears only when iasl cannot determine the type, please avoid using) |
| IntObj | Integer | `External (TEST, IntObj` | `Name (TEST, 0)` |
| StrObj | String | `External (\_PR.MSTR, StrObj` | `Name (MSTR,"ASL")` |
| BuffObj | Buffer | `External (\_SB.PCI0.I2C0.TPD0.SBFB, BuffObj` | `Name (SBFB, ResourceTemplate ()`<br/>`Name (BUF0, Buffer() {"abcde"})` |
| PkgObj | Package | `External (_SB.PCI0.RP01._PRW, PkgObj` | `Name (_PRW, Package (0x02) {0x0D, 0x03 })` |
| FieldUnitObj | Field Unit | `External (OSYS, FieldUnitObj` | `OSYS, 16,` |
| DeviceObj | Device | `External (\_SB.PCI0.I2C1.ETPD, DeviceObj` | `Device (ETPD)` |
| EventObj | Event | `External (XXXX, EventObj` | `Event (XXXX)` |
| MethodObj | Function | `External (\_SB.PCI0.GPI0._STA, MethodObj` | `Method (_STA, 0, NotSerialized)` |
| MutexObj | Mutex | `External (_SB.PCI0.LPCB.EC0.BATM, MutexObj` | `Mutex (BATM, 0x07)` |
| OpRegionObj | Operation area | `External (GNVS, OpRegionObj` | `OperationRegion (GNVS, SystemMemory, 0x7A4E7000, 0x0866)` |
| PowerResObj | Power Resources | `External (\_SB.PCI0.XDCI, PowerResObj` | `PowerResource (USBC, 0, 0)` |
| ProcessorObj | Processor | `External (\_SB.PR00, ProcessorObj` | `Processor (PR00, 0x01, 0x00001810, 0x06)` |
| ThermalZoneObj | Temperature Control Zone | `External (\_TZ.THRM, ThermalZoneObj` | `ThermalZone (THRM)` |
| BuffFieldObj | Buffer | `External (\_SB.PCI0._CRS.BBBB, BuffFieldObj` | `CreateField (AAAA, Zero, BBBB)` |

> DDBHandleObj is almost impossible to encounter, so I will not discuss it here

When externally referencing in SSDT, use `External (` + `path and name` + `,` + `reference type)`, and after positioning the cursor in the referenced Object, MaciASL will display the path in the lower left corner

## ASL Existence Judgment Statement CondRefOf

`CondRefOf` is a very useful function in ASL that can be used to determine the existence of all types of Object

```swift
Method (SSCN, 0, NotSerialized)
{
    If (_OSI ("Darwin"))
    {
        ...
    }
    ElseIf (CondRefOf (\_SB.PCI0.I2C0.XSCN))
    {
        If (USTP)
        {
            Return (\_SB.PCI0.I2C0.XSCN ())
        }
    }

    Return (Zero)
}
```

The above code is excerpted from **`SSDT-I2CxConf`**. When starting a non-macOS system, if there is `XSCN` (formerly `SSCN`, has been renamed to `XSCN`) under `I2C0`, create A new function and use the original name `SSCN`), then return the value returned by the original function

## Conclusion

I’m tired, I’m writing here today. With these foundations, I feel that now everyone is in a daze when facing DSDT and SSDT, at least I can understand a few sentences.
