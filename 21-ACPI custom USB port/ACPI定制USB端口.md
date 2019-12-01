## ACPI定制USB端口

#### 描述

- 本方法通过修改ACPI文件实现USB端口的定制。
- 本方法操作过程需要drop某个ACPI文件。通常情况下，OpenCore **不建议** 这样做，定制USB端口一般使用 ***Hackintool.app*** 工具。
- 本方法献给爱好者们。

#### 适用范围

- XHC以及它的 `_UPC` 存在于单独的ACPI文件中。

####  `_UPC`规范

```
_UPC, Package ()
{
   	xxxx,//xxxx：0xFF表示该端口存在，0x00表示该端口不存在。
   	yyyy,//yyyy：0x00表示usb2.0，0x03表示usb3.0，0xFF表示内置，0x09表示typeC，其他值等。
   	0x00, 
  	0x00
}
```

有关 `_UPC` 详细的内容参见 ACPI 规范。

#### USB定制过程

- 清除其他定制方法的补丁、驱动等。

- drop ACPI文件

  - 确认XHC并包括 `_UPC` 的ACPI文件。如dell5480的 ***SSDT-2-xh_OEMBD.aml*** ，小新PRO13（i5）的 ***SSDT-8-CB-01.aml*** 。

  - `config\ACPI\Block\` 以 `TableLength` （十进制）和 `TableSignature` 方式drop ACPI文件。如：

    **dell5480**： `TableLength` =2001， `TableSignature` =53534454 （SSDT）

    **小新PRO13（i5）**： `TableLength` =12565， `TableSignature` =53534454 （SSDT）

- 定制SSDT补丁文件

  - 将需要drop的原始ACPI文件拖到桌面， **建议：** 

    - 另存为DSL格式。
    - 修改文件名。如： ***SSDT-2-xh_OEMBD_XHC.dsl*** 、 ***SSDT-8-CB-01_XHC.dsl***。 
    - 修改文件内的 `OEM Table ID` 为自己喜欢的名字。
    - 排除错误。

  - SSDT文件中所有端口的 `_UPC` 最前端添加以下代码：

    ```
    Method (_UPC, 0, NotSerialized) 
    {
    		If (_OSI ("Darwin"))
    		{
    				Return (Package ()
    				{
    						xxxx,
    						yyyy, 
    						0x00, 
    						0x00
    				})
    		}
    		//以下是原内容
    		......
    }
    ```

  - 根据`_UPC`规范定制USB端口。即，修正xxxx、yyyy的值。

    - 如果端口不存在
      - xxxx=0x00
      - yyyy=0x00
    - 如果端口存在
      - xxxx=0xFF
      - yyyy
        - 0x00：USB2.0
        - 0x03：USB3.0
        - 0xFF：内置USB设备
        - 0x09：typeC
        - 其他值，参考ACPI 规范
  
  - 排错、编译、补丁文件放至 `ACPI` 、添加补丁列表。

#### 参考示例

- ***SSDT-2-xh_OEMBD_XHC*** 
- ***SSDT-8-CB-01_XHC*** 

