# Add missing parts

## Description

Adding missing parts is just a perfect solution, not necessary!

### Instructions for use

**DSDT:**

- Search for `PNP0200`, if it is missing, add ***SSDT- DMAC***.
- Search for `MCHC`, if it is missing, add ***SSDT- MCHC***.
- Search for `PNP0C01`, if it is missing, add ***SSDT- MEM2***.
- 6th and 7th generation machines, search for `0x001F0002`, if it is missing, add ***SSDT- PPMC***.
- 8th generation, 9th generation + machine, search for `PMCR`,` APP9876`, if missing, add `SSDT- PMCR`. (Credits `Please call me official`)
- Search for `PNP0C0C`, if it is missing, add *** SSDT- PWRB ***.
- Search for `PNP0C0E`, if it is missing, add *** SSDT- SLPB ***.
- 9th generation + machine, search for `XSPI`,` 0x001F0005`, if missing, add `SSDT- XSPI`.

### Attention

When using `SSDT- DMAC`, you should pay attention to the` LPC` name. `LPCB` is used in the sample. If your machine is` LCP`, please modify `LPCB` in the patch to` LPC`.
