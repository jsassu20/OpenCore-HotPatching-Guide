# ACPI Binary Rename

In order to achieve the patch effect, we have performed a binary renaming of ACPI. It can be said that this is a special and last resort solution. It can also be considered that this method is more convenient, faster, and more efficient.

## Example

Take the example of enabling `HPET`. We want its `_STA` to return `0x0F`.

Binary rename:

Find: `A0 08 48 50` [Note: `A0`=`If`]

Replace: `A4 0A 0F A3` [Note: `A4 0A 0F` =`Return (0x0F)`; `A3`=Noop, used to fill in the number of bytes]

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

  This is an illustrative example. In reality, we should try our best to ensure the completeness of the grammar after the name change. Below is the complete `Find`, `Replace` data.
  
  Find: `A0 08 48 50 54 45 A4 0A 0F A4 00`
  
  Replace: `A4 0A 0F A3 A3 A3 A3 A3 A3 A3 A3`
  
  Complete code after `Find` and `Replace`
  
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

  The method described in this article is not a rename of `Device` or `Method` in the usual sense, but a patch implemented by rename. This requires that the number of `Find` is only one, **unless** our intention is to perform the same operation of `Find` and `Replace` in multiple places.

  **Special attention**: In order to ensure the correctness of the `Find` data, any rewriting of a piece of code to find confirmed binary data is very unreliable!

-`Replace` number of bytes

  The number of bytes in `Find`, `Replace` must be equal. For example, if `Find` is 10 bytes, then `Replace` is also 10 bytes. If `Replace` is less than 10 bytes, fill it with `A3` (no operation).

## `Find` data search method

Usually, use binary software (such as `010 Editor` and `MaciASL.app`) to open the same `ACPI` file, use binary data or text `Find` related content, observe the context, I believe you can quickly determine `Find` data.

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

 Updating the BIOS may cause the name change to become invalid. The greater the number of `Find` bytes, the greater the possibility of failure.

### Attachment: TP-W530 prohibits BAT1

Find: `00 A0 4F 04 5C 48 38 44 52`

Replace: `00 A4 0A 00 A3 A3 A3 A3 A3`

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
          Return (0x00)
          Noop
          Noop
          Noop
          Noop
          Noop
          If (HB1A)
          ...
    }
  ```
