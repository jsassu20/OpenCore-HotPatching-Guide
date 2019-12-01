# ACPI Binary Rename

In order to achieve the patch effect, we renamed ACPI in binary. It can be said that this is a special and last resort solution, and this method can be considered more convenient, fast and efficient.

Example:

Take enabling HPET, we want to have:

`_STA return 0x0F`

Binary rename:

Find: `A0 08 48 50` [**Note:** `A0 = If`]

Replace: `A4 0A 0F A3` [**Note:**`A4 0A 0F` = `Return (0x0F)`, `A3` = Noop, used to complete the number of bytes]

- **Original code**

```
  Method (_STA, 0 , NotSerialized)
  {
      If (HPTE)
      {
          Return ( 0x0F )
      }
      Return (Zero)
  }
```
- **Renamed code**

```
Method (_STA, 0 , NotSerialized)
    {
        Return ( 0x0F )
        Noop
        TE ** ()
         Return (Zero)
  }
```
  
Explanation : An obvious error occurred after the name was changed, but this error is not harmful. First, the Return (0x0F)latter content will not be executed. Second, the error is located {}within no effect on other content.

This is an illustrative example. In fact, we should try to ensure the integrity of the grammar after the rename. Here is the complete Find, Replacedata.

Find: `A0 08 48 50 54 45 A4 0A 0F A4 00`

Replace: `A4 0A 0F A3 A3 A3 A3 A3 A3 A3 A3`

Complete `Find`, `Replace` post code

```
  Method (_STA, 0 , NotSerialized)
  {
      Return ( 0x0F )
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

- **ACPI** original file

 `Find` The binary file must be the original ACPI file, and cannot be modified or saved by any software. In other words, it must be the original binary file provided by the machine.

 `Find` Uniqueness, correctness

 The methods described herein are not of the usual sense `Device` or `Method` renamed, renamed by a patch implemented. This requires that Findthe number is only one, unless our intention is to implement multiple `Find` and `Replace` the same operation.

- **Special attention** : In order to ensure the `Find` accuracy of the data, rewrite any piece of code and find confirmation from binary data, so can not be trusted!

- `Replace` Number of bytes

- `Find` The `Replace` number of bytes must be equal. For example, Find10 bytes, Replace is 10 bytes. If it is Replaceless than 10 bytes, it is A3filled with (no operation).

## Find Data lookup method
Typically, software, binary (e.g., `010 Editor` and MaciASL.app) the same ACPIfile in a binary manner or text data `Find` content, context observation, will soon be able to determine `Find` the data.

## Replace content
"Requirement" in the description `Find`, the [rewrite any piece of code and look for confirmation from binary data, so can not be trusted! ], However, Replacecan do this. Following the example above, we write a piece of code:

    DefinitionBlock ( " " , " SSDT " , 2 , " hack " , " 111 " , 0 )
    {
        Method (_STA, 0 , NotSerialized)
        {
            Return ( 0x0F )
        }
    }
Open the compiled binary software, found that: XX ... `5F 53 54 41 00 A4 0A 0F` where `A4 0A 0F` is `Return (0x0F)`.

Note: The `Replace` content should follow the ACPI specification and ASL language requirements.

##Precautions

Updating the BIOS may cause the rename to fail. FindThe larger the number of bytes, the greater the possibility of failure.

###Attachment: TP-W530 prohibits BAT1

Find: `00 A0 4F 04 5C 48 38 44 52`

Replace: `00 A4 0A 00 A3 A3 A3 A3 A3`

- **Original Code**

```
  Method (_STA, 0 , NotSerialized)
  {
        If (\ H8DR)
        {
            If (HB1A)
            {
            ... 
  }
```  
- **Renamed Code**

```
  Method (_STA, 0 , NotSerialized)
    {
        Return ( 0x00 )
        Noop
        Noop
        Noop
        Noop
        Noop
        If (HB1A)
         ... 
  }
``` 
