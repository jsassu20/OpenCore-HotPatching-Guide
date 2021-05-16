# CMOS related

## CMOS reset patch

### Description

-Some machines will appear **"Power-on self-test error"** when shutting down or restarting, which is caused by the resetting of CMOS.
-When using Clover, check `ACPI\FixRTC` to solve the above problems.
-When using OpenCore, the following solutions are officially provided, see ***Sample.plist***:
  -Install **RTCMemoryFixup.kext**
  -`Kernel\Patch` patch: **__ZN11BCM5701Enet14getAdapterInfoEv**
-This chapter provides an SSDT patch method to solve the above problems. This SSDT patch is essentially a counterfeit RTC, see "Preset Variable Method" and "Counterfeit Equipment".

### solution

See "15-1-CMOS Reset Patch" for details.

## **CMOS** Memory and RTCMemoryFixup

-When **AppleRTC** and **BIOS** conflict, you can try to use **RTCMemoryFixup** to simulate **CMOS** memory to avoid the conflict.
-**RTCMemoryFixup** download link: <https://github.com/acidanthera/RTCMemoryFixup>

### **CMOS** Memory

-**CMOS** The memory stores important data such as date, time, hardware configuration information, auxiliary settings information, startup settings, and sleep information.
-Some **CMOS** memory space definitions:
  -Date, time: `00-0D`
  -Sleep information storage area: `80-AB`
  -Power management: `B0-B4`
  -Other

### Simulation **CMOS** memory method

See "15-2-CMOS Memory and RTCMemoryFixup" for details.
