# Installing Zesp32 on firmware with openwrt

Zesp will work the same as on the stock firmware, but due to the difference in system
libraries, additional steps are required

### 1. Installing nodejs and zesp32

Install packages on the gateway

```shell script
wget https://github.com/devbis/xiaomi-gateway-openwrt/raw/master/files/node_v10.22.0-1_arm_cortex-a9_neon.ipk
wget https://github.com/devbis/xiaomi-gateway-openwrt/raw/master/files/node-npm_v10.22.0-1_arm_cortex-a9_neon.ipk
wget https://github.com/devbis/xiaomi-gateway-openwrt/raw/master/files/node-zesp32_20200928-1_arm_cortex-a9_neon.ipk
opkg install node_v10.22.0-1_arm_cortex-a9_neon.ipk
opkg install node-npm_v10.22.0-1_arm_cortex-a9_neon.ipk
opkg install node-zesp32_20200928-1_arm_cortex-a9_neon.ipk
```

### 2. Zigbee firmware.

If you already have a zesp version, you can skip this step.

Stop zesp32

```shell script
killall node
```

To flash on openwrt enter the command

```shell script
sh /root/flash.sh /opt/zesp32/util/Zigbee.bin --erasepdm
```

This will update the firmware in the jn5169 module
Now you can start zesp32 again

```shell script
/etc/init.d/zesp32 restart
```

Zesp32 interface will be available at `http://*GATEWAY_IP*:8081/`
