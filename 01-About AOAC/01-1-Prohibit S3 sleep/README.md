# Prohibit `S3` sleep

## Description

-**S3`Sleep forbidden** is used to solve the **sleep failure** problem caused by some machines for some reason. **Sleep failure** means: the machine cannot be awakened normally after sleep, it is shown as a crash, restarting, shutting down after waking, etc.

## Patch method

-Renamed: `_S3 to XS3`

   ```text
   Find 5F53335F
   Replace 5853335F
   ```

-Patch
   -***SSDT-NameS3-disable***: It is suitable for `ACPI` to describe the method of `S3` as the `Name` type. This is the case for most machines.
   -***SSDT-MethodS3-disable***: Applicable to `ACPI` to describe the method of `S3` as the type of `Method`.

## Precautions

-Choose the corresponding patch according to the machine's original `ACPI` description `S3` method.
