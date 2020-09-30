# Installing alternative OpenWrt firmware on DGNWG05LM and ZHWG11LM gateways

The instruction applies only to the European version of the gateway from xiaomi mieu01,
with a European plug, as well as a version of the gateway from Aqara ZHWG11LM with a Chinese or
European plug. For xiaomi gateway2 version with Chinese plug
DGNWG02LM it will not work, it has other hardware components installed.

This instruction assumes that you already have ssh access to the gateway.
If you have not done this, use the instructions

[https://4pda.ru/forum/index.php?act=findpost&pid=99314437&anchor=Spoil-99314437-1](https://4pda.ru/forum/index.php?act=findpost&pid=99314437&anchor=Spoil-99314437-1)


## Backup copy
Make a backup. If you decide to return to
original firmware, to restore you need tar.gz with archive
root filesystem.

```shell script
tar -cvpzf /tmp/lumi_stock.tar.gz -C / --exclude = '. / tmp / *' --exclude = '. / proc / *' --exclude = '. / sys / *'.
```

After the backup is done, download it to your local computer

```shell script
scp root @ * GATEWAY_IP *: / tmp / lumi_stock.tar.gz.
```

or using WinScp in `scp` mode

If you already have a rootfs image made with dd, ** make an archive anyway **.
During the boot phase of the dd image, nand flash or ubifs errors usually occur. Option
with tar.gz does not have these drawbacks because it formats the flash before loading.

## Solder usb + uart

To modify the firmware you need to make
 hardware modifications, solder 7 wires to the gateway itself
- 3 wires for the usb2uart adapter (you did this at the stage of receiving the root)
- 4 wires per usb connector or a wire with a usb plug at the end.
 It is enough to solder 4 wires, + 5v, d +, d- and gnd.
 ID wire is not used
 Check that d + and d- are not reversed, otherwise the device will not be detected

![Pinout of UART and USB on the gateway](images/gateway_pinout.jpg "How to sold wires")


## Firmware

We have prepared an archive with the mfgtools program for downloading the firmware to the gateway,
as well as the firmware itself. The archive includes a program for windows
and a console application for linux

[version 0.1.0rc4](files/mfgtools-rc4.zip)

### Unpacking the firmware

So that after the firmware the gateway immediately connects to your wifi network,
edit the file
`Profiles / Linux / OS Firmware / files / wireless`
and enter your wifi network name and password in the last section.

    config wifi-iface 'wifinet1'
        option ssid 'YOUR_SSID'
        option key 'YOUR_PASSWORD'

Leave the rest of the parameters as they are.

### Connect the gateway to your computer

You need to connect the gateway with two cables to your computer. UART and USB.
USB at this stage will not be detected in the computer.
To connect to the gateway console, for windows use
the PuTTY program and use the COM port that appeared for usb2uart.
For linux use any terminal program like
`picocom / dev / ttyUSB0 -b 115200`


### Switch to USB download mode

In order to switch to the firmware mode, you need to start the gateway in
console on the serial port abort uboot by pressing
any button. You will have 1 second for this. A prompt for teams will appear

    =>

Next, on the uboot command line, you need to enter

    bmode usb

And press enter.
After that, the gateway will switch to usb boot mode and mfgtools will be able to update
sections in the memory of the gateway.

![Switch to USB Boot Mode](images/bmode_usb.png "Switch to USB Boot Mode")

If you have Windows, you may need to install drivers,
from the Drivers folder.

Run mfgtools.

#### Windows
In case of windows, a window will open. If everything is soldered correctly and the driver
are set correctly, then the line in the program will say
HID-compliant device
![Mfgtools](images/mfgtools_win.png "Mfgtools")


You need to press the Start button to start the firmware.

After the end of the firmware, when the progress bar reaches the end and
turns green, you need to press Stop. If this is not done, after a few
minutes, the program will restart the firmware, and this will lead to an error. If such
happened, restart the gateway and repeat the procedure starting the transfer
gateway to `bmode usb`.

#### Linux

Go to the firmware folder. Run the console utility as superuser

```shell script
sudo ./mfgtoolcli -p 1
```

The pseudo-graphic interface will display the stages of the firmware

![Mfgtools](images/mfgtools_lin.png)

When the gateway is connected and the hid device is detected, the program will immediately start
firmware process. If the process does not go through, check that the device is connected and
detected in the output of the `dmesg` command


### Flashing process
You can also follow the firmware stages in the output console of the gateway itself.
At the end of the firmware, the console will display

    Update Complete!

![Update complete](images/update_complete.png)

After that, you can reboot the gateway. Unplug it and plug it back in.



### Using OpenWrt

The system is configured to immediately connect to your wifi network,
in case you edited the wireless file before flashing.
If you make a mistake, edit the parameters in the file `/ etc / config / wireless`
already on the device.

After the firmware, the mac address of the gateway changes, because the ip address is also most likely
will change. Check it in the router or in the gateway itself.

# Don't forget to connect the antennas!

Otherwise, problems with connecting to the network are provided

The gateway is pre-installed:
- OpenWrt LuCi GUI on port 80 http
- Domoticz at 8080 port
  - Plugin for Domoticz to work with firmware Zigbee [Zigate] (https://github.com/pipiche38/Domoticz-Zigate)
  - The web interface for working with Zigate for domoticz will be available on port 9440
- command utility for flashing zigbee module jn5169

Do not enable WiFi AP + Station modes on the gateway at the same time.
The driver that is used in the system cannot work in two modes
at the same time.
If you changed the LuCi settings and after that the gateway stopped connecting to the network,
edit the file `/ etc / config / wireless` to match what is
in the archive.

### Working with Zigbee

1. [Configuring zigate plugin] (./ zigate.md)
2. [Install Zesp32] (./ zesp32.md)

### Return to stock firmware

Modification with openwrt only affects the modification of the root filesystem;
bootloader, kernel and dtb remain original. Therefore, to return to stock you need
flash the original file system from the backup.

Put the file `lumi_stock.tar.gz` in the folder` Profiles / Linux / OS Firmware / files`
next to the openwrt and wireless firmware file
Edit the file `Profiles / Linux / OS Firmware / ucl2.xml`
with a text editor, and replace

```xml
<CMD state = "Updater" type = "push" body = "pipe tar -zxv -C / mnt / mtd3" file = "files / rc4-domoticz-openwrt-imx6-rootfs.tar.gz" ifdev = "MX6UL MX7D MX6ULL "> Sending and writing rootfs </CMD>
```

to the line with the path to the archive file with the original firmware

```xml
<CMD state = "Updater" type = "push" body = "pipe tar -zxv -C / mnt / mtd3" file = "files / lumi_stock.tar.gz" ifdev = "MX6UL MX7D MX6ULL"> Sending and writing rootfs < / Cmd>
```

Then again put the gateway into the boot mode via usb and via mfgtools
flash the original firmware.


## Links

1. An article that details the changes and technical modifications:
[Xiaomi Gateway (eu version - Lumi.gateway.mieu01) Hacked] (https://habr.com/ru/post/494296/)
2. Collection of information on hardware and software moding Xiaomi Gateway [https://github.com/T-REX-XP/XiaomiGatewayHack ](https://github.com/T-REX-XP/XiaomiGatewayHack)
2. Telegram channel with discussion of modifications [https://t.me/xiaomi_gw_hack ](https://t.me/xiaomi_gw_hack)