# STM32烧录micropython固件

## **准备工作**

安装dfu模式烧录工具

sudo apt install dfu-util

进入dfu烧录模式

上电状态下，同时按下BOOT0键和RST键

先松开RST键，0.5s后松开BOOT0键（延时不能太久）

lsusb就会看到“STMicroelectronics STM Device in DFU Mode”

进入dfu模式未必能够一次成功。那么就多试几次

## **固件烧录**

### **dfu-util**

First, install dfu-util via your package manager.

You can list all DFU-capable devices using the `-l` argument:

```Plain
$ sudo dfu-util -l
dfu-util 0.5

(C) 2005-2008 by Weston Schmidt, Harald Welte and OpenMoko Inc.
(C) 2010-2011 Tormod Volden (DfuSe support)
This program is Free Software and has ABSOLUTELY NO WARRANTY

dfu-util does currently only support DFU version 1.0

Found DFU: [0483:df11] devnum=0, cfg=1, intf=0, alt=0, name="@Internal Flash  /0x08000000/04*016Kg,01*064Kg,07*128Kg"
Found DFU: [0483:df11] devnum=0, cfg=1, intf=0, alt=1, name="@Option Bytes  /0x1FFFC000/01*016 e"
Found DFU: [0483:df11] devnum=0, cfg=1, intf=0, alt=2, name="@OTP Memory /0x1FFF7800/01*512 e,01*016 e"
Found DFU: [0483:df11] devnum=0, cfg=1, intf=0, alt=3, name="@Device Feature/0xFFFF0000/01*004 e"
```

If desired, backup the original firmware (this doesn't work with dfu-util 0.5, but does with 0.7 or newer). Previous releases of micropython are available from: http://micropython.org/download/ so performing a backup shouldn't be necessary.

```Plain
$ sudo dfu-util --alt 0 --upload pyboard-original.dat -s:524288
(...)
Starting upload: [#######] finished!
```

Then write the downloaded firmware to the pyboard:

```Plain
$ sudo dfu-util --alt 0 -D pybv10-2014-05-19-v1.0.1-24-g5cdff5f.dfu 
(...)
done parsing DfuSe file
```

If you need to restore the original firmware, use the following command:

```Plain
$ sudo dfu-util --alt 0 -D pyboard-original.dat -d 0483:df11 -s 0x8000000
```

This will restore everything, including the internal flash filesystem.

### **查看固件版本**

Once you have flashed the new firmware you may want to check it correspond to the version expected. At the REPL prompt, type

```Plain
>>> import os
>>> os.uname()
```