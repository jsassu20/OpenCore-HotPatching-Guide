# ACPI Form

### Brief description

-ACPI (Advanced Configuration & Power Interface) is a computer's advanced configuration and power interface. It is a power management standard jointly developed by Intel, Microsoft and other manufacturers, namely [`ACPI Specification`](https://www.acpica. org/documentation). Each computer will be provided with a set of binary files conforming to the [`ACPI specification`](https://www.acpica.org/documentation) when it leaves the factory. These files are called ACPI forms. The number and content of ACPI tables vary from device to device, and **may** vary with the BIOS version. The ACPI form includes:
  -1 DSDT.aml
  -Multiple SSDT-***.aml. Such as: `SSDT-SataAhci.aml`, `SSDT-CpuSsdt.aml`, `SSDT-CB-01.aml` etc.
  -Other aml. Such as: `APIC.aml`, `BGRT.aml`, `DMAR.aml`, `ECDT.aml`, etc.
-Some tools and booters can extract the ACPI form of the machine, such as Aida64 under windows, clover and so on. Because the ACPI form is a binary file, we need a decompiler to help us understand the content of the file, such as MaciASL of the MAC system. When opening these forms with decompilation software, especially when opening DSDT.aml, many errors may appear. It should be noted that in most cases, these errors are caused by the software decompilation process, and the ACPI sheet provided by the machine does not contain these errors.
-The ACPI form describes the hardware information of the machine in the form of aml language, and it does not have any driving capability. However, the normal operation of a certain hardware requires correct ACPI, and the wrong description method can lead to boot failure or system crash. For example, if a Broadcom network card is installed in the machine, if ACPI is described as an Intel network card, then the system will load the Intel network card driver, which is obviously wrong. For another example, the machine does not provide hardware for `automatic brightness adjustment`, even if ACPI adds `SSDT-ALS0`, it cannot realize the automatic brightness adjustment function.
-Due to the different working principles of the windows system and the MAC system, the black apple needs to correct the ACPI. The correct ACPI is the basis for the stable work of the Black Apple. **Strongly recommended** Use `Hot Patch`[HOTpatch] to apply a patch to ACPI. `Hot Patches` can avoid so-called DSDT errors.
-For details of ACPI, please refer to [`ACPI Specification`](https://www.acpica.org/documentation); for introduction of aml language, please refer to "ASL Grammar Basics".
-This chapter `ACPI Patch` only applies to `OpenCore`

### ACPI Patch

-DSDT patch and SSDT patch

  This part of the content refers to other chapters of "OC-little".

-Other form patches

  -**Clear ACPI's `header fields`**
    -**Patch method**: `ACPI\Quirks\NormalizeHeaders` = `true`
    -**Note**: This patch is only required for Mac 10.13
  -**Relocate ACPI memory area**
    -**Patch method**: `ACPI\Quirks\RebaseRegions` = `true`
    -**Explanation**: The memory area of ​​the ACPI sheet has both dynamically allocated addresses and fixedly allocated addresses. The function of the patch is to **relocate the ACPI memory area**, this operation is very dangerous, unless this patch can solve the boot crash problem, otherwise do not use it.
  -**FACP.aml**
    
    -**Patch method**: `ACPI\Quirks\FadtEnableReset` = `true`
    
    -**Description**: [`ACPI Specification`](https://www.acpica.org/documentation) Use **FADT** to define various static system information related to configuration and power management. ACPI form appears as **FACP.aml** form. **FACP.aml** The information represented by the form includes RTC clock, power and sleep buttons, power management, etc. The following aspects are currently related to black apples:
    
    -If the restart and shutdown are abnormal, try to use this patch
      
    -If you cannot call out the "Restart, Sleep, Cancel, Shutdown" menu by pressing the **power button**, try using this patch
      
      **Note**: If `ACPI\Quirks\FadtEnableReset` = `true` still cannot call the "Restart, Sleep, Cancel, Shutdown" menu, try to add ***SSDT-PMCR***. ***SSDT-PMCR*** is located in "Add Missing Parts" on OC-little.
      
    -The `Low Power S0 Idle` and `Hardware Reduced` in the **FACP.aml** form represent the machine type and determine the power management method. If `Low Power S0 Idle` = `1`, it means that the machine belongs to `AOAC`. For the content of `AOAC`, please refer to "About AOAC".
    
  -**FACS.aml**
    -**Patch method**: `ACPI\Quirks\ResetHwSig` = `true`
    -**Description**: The `Hardware Signature` item in the **FACS.aml** form is a 4-byte hardware signature, which is calculated based on the basic hardware configuration after the system is booted. If this value changes after the machine is in the **sleep** state **waking up**, the system will not be able to recover correctly. The purpose of the patch is to make `Hardware Signature` = `0` to try to solve the above problems.
    -**Note: **If the system has disabled **hibernation**, don't bother about the patch
  -**BGRT.aml**
    -**Patch method**: `ACPI\Quirks\ResetLogoStatus` = `true`
    -**Description**: **BGRT.aml** form is a guide graphic resource form. According to [`ACPI Specification`](https://www.acpica.org/documentation), the `Displayed` item of the form should = `0`. However, some manufacturers have written non-zero data in the `Displayed` item for some reason, which may cause the screen refresh failure during the boot phase. The effect of the patch is to make `Displayed` = `0`.
    -**Note:** Not all machines have this form
  -**DMAR.aml**
    -**Patch method**: `Kernel\Quirks\DisableIoMapper` = `true`
    -**Note**: The function of the patch is the same as that of BIOS prohibiting `VT-d` or Drop **DMAR.aml**
    -**Note**: This patch is only required for earlier Mac systems
  -**ECDT.aml**
    
    -**Patch method**: Globally rename the `EC` name and path of all ACPI forms to be consistent with `Namepath`
    -**Explanation**: The namepath of the **ECDT.aml** form of individual machines (such as **Lenovo yoga-s740**) is inconsistent with the `EC` names of other ACPI forms, which will cause the machine to boot An ACPI error occurred during the process. This patch method can better solve the ACPI error reporting problem.
    -**Note**: Not all machines have this form
