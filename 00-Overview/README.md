#ACPI name change and patch

* Try to avoid or use fewer renames and patches. Such HDAS rename HDEFas: EC0 rename EC, , SSDT-OC-XOSIand so on. Especially underlined MethodObjchanged its name (such as _STA, _OSI, etc.) should be used with caution . In general:

 * No operating system patches are required. For components that are restricted by the system and cannot work normally, customize patches according to the specific conditions of ACPI. There are special requirements for the operating system of prudent use of "operating system patches."

 * Some machines do not require the use of brightness shortcuts for renaming and patching. Use "PS2 keyboard mapping @ OC-xlivans" to achieve the same effect.

 * For now, the vast majority of machines need "0D6D patch" to solve 秒醒the problem.

 * For the battery part, if the data must be split, battery renaming and patching are essential.

 * Most Thinkpad machines need "PTSWAK Comprehensive Patch and Extended Patch" to solve the problem that the breathing light does not recover after waking up.

 * For the machine with the sleep button [Little Moon], please refer to "PNP0C0E Sleep Correction Method" for the system crash when this button is pressed.

* Solving some problems requires enabling or disabling certain devices. To enable or disable the device:
 
 * "ACPI Binary Rename"-This method is very effective for single systems. For multiple systems, the impact of the rename on other systems should be assessed. Use with caution .
 
 * "Preset variable method"-may affect other components or other systems. Use with caution .
 
 * Counterfeit Devices-This method is very reliable. Recommended .

#Important patch

* SSDT-RTC0 -located in Counterfeit Devices

Some machines have crashed during the startup phase because RTC [PNP0B00] is disabled. Using SSDT-RTC0 can solve this problem.

* SSDT-EC -Located in Counterfeit EC

For 10.15+ system [notebook], if the EC controller name not ECneed to add SSDT-EC , otherwise the start-up phase system crashes.

