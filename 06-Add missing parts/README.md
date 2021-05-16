# Add missing parts

## Description

Adding missing parts is just a perfect solution, not necessary!

### Instructions for use

**DSDT:**

-Search for `PNP0200`, if it is missing, add ***SSDT-DMAC***.

-Search for `MCHC`, if it is missing, add ***SSDT-MCHC***.

-Search for `PNP0C01`, if it is missing, add ***SSDT-MEM2***.

-Search for `0x00160000`, if it is missing, add ***SSDT-IMEI***.

-For machines of 6th generation and above, search for `0x001F0002`, if it is missing, add ***SSDT-PPMC***.

-For machines of 6th generation and above, search for `PMCR` and `APP9876`, if missing, you can add ***SSDT-PMCR***.

  Note: @请叫我官人 Provide a method, which has become the official SSDT example of OpenCore.
  > Z390 chipset PMC (D31:F2) can only be started via MMIO. Since there is no PMC device in the ACPI specification, Apple introduced its own name `APP9876` to access this device from the AppleIntelPCHPMC driver. In other operating systems, generally use `HID: PNP0C02`, `UID: PCHRESV` to access this device.
  > Platforms including APTIO V cannot read or write NVRAM (frozen in SMM mode) before initializing the PMC device.
  > Although I don't know why this happens, it is worth noting that PMC and SPI are located in different memory areas. PCHRESV maps these two areas at the same time, but Apple's AppleIntelPCHPMC only maps the area where PMC is located.
  > There is no relationship between the PMC device and the LPC bus. This SSDT is purely to speed up the initialization of the PMC and add the device to the LPC bus. If it is added to the PCI0 bus, PMC will only start after the PCI configuration is completed, which is too late for the operation that needs to read NVRAM.

-Search for `PNP0C0C`, if it is missing, add ***SSDT-PWRB***.

-Search for `PNP0C0E`, if it is missing, you can add ***SSDT-SLPB***, "PNP0C0E Sleep Correction Method" requires this component.

### Note

When using some of the above patches, please note that the name of `LPCB` should be consistent with the original ACPI name.
