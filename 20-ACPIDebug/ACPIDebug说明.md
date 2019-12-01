## ACPIDebug说明

#### 描述

通过在 ***SSDT-xxxx*** 补丁里添加调试代码，能够在控制台上看到补丁或者ACPI的工作过程，用于调试补丁。

#### 驱动

- ***ACPIDebug.kext*** 

  安装 ***ACPIDebug.kext*** 至OC\Kexts，并添加驱动列表。

#### 补丁

- ***SSDT-RMDT***——基本补丁

- ***SSDT-TEST-NEVT-dell***——dell机器检测Fn功能键补丁样本

  更名：NEVT to XEVT

  ```
  // Find:     4E 45 56 54 00
  // Replace:  58 45 56 54 00
  ```

  添加 ***SSDT-RMDT*** 、 ***SSDT-TEST-NEVT-dell*** 至OC\ACPI，并添加补丁列表。

#### 调试

- 打开 <u>控制台</u> ，搜索“AAA001”。见 ***SSDT-TEST-NEVT-dell*** 。
- 按下某功能键，观察控制台的反映情况。

#### 备注

​	Debug调试代码可以多样化，如：\RMDT.P1、\RMDT.P2、\RMDT.P3等等，详见 ***SSDT-RMDT.dsl*** 。

##### 以上主要内容来自@RehabMan

