# ThinkPad machine special patch

## Special rename

Like some Lenovo machines, the DSDT of ThinkPad machines may also contain the `PNLF` field. Search for `PNLF` in DSDT, if there is `PNLF`, you need to add the following rename:

```text
Find: 504E4C46
Replace: 584E4C46
```

## Special Patch

### ThinkPad touchpad attribute injection and small red dot anti-drift patch

ThinkPad's touchpad and red dot belong to the ELAN type, and use the `Synaptics` protocol to connect through SMBus. Since there is currently no SMBus driver that can be used stably under macOS, only VoodooPS2 can be used. When using VoodooPS2, in order to enable the ThinkPad optimization built in VoodooPS2, you need to inject touchpad attributes through SSDT.

**SSDT-ThinkPad_ClickPad**

If your trackpad is one of the two shown in the image below, then please use this patch.

![](https://i.loli.net/2020/04/26/ceEyQfgikqzjapL.png)

**SSDT-ThinkPad_TouchPad**

If your trackpad is one of the two shown in the image below, then please use this patch.

![](https://i.loli.net/2020/04/26/FUxIp4nmAb2PSws.png)

----

The keyboard path of most ThinkPad machines is `\_SB.PCI0.LPC.KBD` or `\_SB.PCI0.LPCB.KBD`. The two provided patches use `_SB.PCI0.LPC.KBD` by default. Please check the original LPC bus name and keyboard name against DSDT by yourself, and replace the ACPI path against it.

Both patches involve the modification of the RMCF variable of the keyboard device. If you use the PS2 mapping patch in the "PS2 Keyboard Mapping" chapter at the same time, you need to manually merge the contents of the RMCF variables. For the merge example, please see **SSDT-ThinkPad_ClickPad+PS2Map-AtoZ**. This patch also includes the injection of the ThinkPad ClickPad attribute And A -> Z PS2 Map mapping.

In addition, Rebhabman's VoodooPS2Controller is outdated. It is recommended to use acidanthera's [VoodooPS2](https://github.com/acidanthera/VoodooPS2), and use [VoodooInput](https://github.com/acidanthera/VoodooInput) to enable ThinkPad All touchpad gestures.

----

The following is an explanation of the various configurations in SSDT, which was written by [@SukkaW](https://github.com/SukkaW) based on [README](https://github.com/RehabMan/OS- X-Voodoo-PS2-Controller/blob/master/README.md) and the code comments in VoodooPS2 are sorted out.

-DragLockTempMask: temporary drag lock shortcut key. `0x40004` corresponds to Control, `0x80008` corresponds to Command, and `0x100010` corresponds to Option. It should be noted that these are the original relationships mapped on the physical keyboard and are not affected by the order of the function keys set in the "System Preferences".
-DynamicEWMode: Dynamic EW mode. In EW mode, two-finger gestures (such as two-finger swipe) will equally divide the bandwidth of the touchpad. When the dynamic EW mode is enabled, the touchpad will not always be in EW mode, so it can improve the two-finger scrolling response speed of the ThinkPad ClickPad touchpad. The control panel and then the touchpad only feedback the direction and distance of one finger sliding, saving the bandwidth of the other finger). While dragging the file (Translator's Note, one finger holds the touchpad and the other finger slides) ClickPad will be pressed, and the EW mode will still be enabled. This option caused problems with a small number of touchpads, so it is disabled by default.
-FakeMiddleButton: Simulate a middle button click when you tap the trackpad with three fingers at the same time.
-HWResetOnStart: Some touchpad devices (especially the ThinkPad touchpad and the little red dot) need to enable this option to work properly.
-ForcePassThrough and SkipPassThrough: The PS2 input device can send a special type of 6-byte data packet of "Pass Through", which can achieve interleaved signal transmission between the touchpad and the pointing stick (such as the ThinkPad red dot). VoodooPS2 has realized the automatic recognition of PS2 device type "Pass Through" packets, these two options are only for debugging purposes.
-PalmNoAction When Typing: The palm of your hand may accidentally touch the touchpad when typing. Enabling this option will prevent accidental touches.
-SmoothInput: After enabling this option, the driver will average every three sampling points to obtain a smooth movement trajectory.
-UnsmoothInput: After enabling this option, when inputting to the touchpad is stopped, the sampling average will be cancelled, and the position when inputting stopped is used as the end position of the track. This is because at the end of the trajectory, sampling averaging may cause large errors or even reverse trajectories. By default, this option and SmoothInput are enabled at the same time.
-DivisorX and DivisorY: Set the edge width of the touchpad. The edge area will not provide any response.
-MomentumScrollThreshY: Use the touchpad to scroll with two fingers, and continue to scroll after the fingers leave the touchpad, as if there is inertia. This option is enabled by default to mimic the trackpad experience of Mac devices as much as possible.
-MultiFingerHorizontalDivisor and MultiFingerVerticalDivisor: Some touchpads have a special "slide bar" area reserved on the far right and/or bottom. This part of the area does not respond to multi-finger swipes by default. These two options provide settings for the width of the non-responsive area. The default value is 1, which means that the entire touchpad can respond to multi-finger swipes.
-Resolution: The "resolution" of the touchpad, the unit is the number of pixels per inch, that is, how many pixels will be drawn on the screen when a finger slides one inch on the touchpad.
-ScrollDeltaThresh: Tolerance value, used to avoid the jitter problem when sliding with two fingers on macOS 10.9 Maverick. The default value is 10.
