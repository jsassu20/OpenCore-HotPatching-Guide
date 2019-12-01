# EFI sample description

## EFI samples are customized according to a single system, <u> not included </ u> The following:

- Patch section

   - Operating system patches

   - X86 Patch
   - PNLF patch
   - I2C Custom Patch

- Drive

   - Wireless LAN, Bluetooth
   - Ethernet
   - USB customization



## Complement the following

- Add your own patch to OC \ ACPI

- Add your own driver to `OC \ Kexts`
- Modify config
   - Added patch list in `ACPI \ Add` and` ACPI \ Patch` sections
   - `Kernel \ Add` Add driver list
   - Migrate `lovers \ Properties` content to` DeviceProperties \ Add`
   - Fill your own model parameters into `PlatformInfo \ Generic`
   - Other settings that may need to be modified

- Custom USB port