# Zigate firmware and connecting Zigate plugin to Domoticz

For the jn5169 module installed on our gateway, the ZiGate firmware is used.
It is already included in the archive. To flash it into the module, use the command

```shell script
sh /root/flash.sh /root/zigate.bin --erasepdm
```

If you have previously flashed the module with Zigate or Zesp firmware, this step can be skipped. Zesp and Zigate are compatible with the zigate plugin.

## Plugin setup

All you need to do is add an element with the type
`Zigate plugin`, enter a name in the Name field and click `Add`

![Adding Zigate plugin](images/zigate_plugin.png)

after a few minutes the plugin will be initialized and launch the control panel on port 9440.


## Zigate Control Panel

![Control Panel](images/zigate_dashboard.png)

In this panel you can enable pairing mode with child devices and
change settings.
To enable add device mode, turn on the switch
`Accept new hardware` in the upper right corner.


You can read more about the panel on the plugin page
[https://github.com/pipiche38/Domoticz-Zigate-Wiki/blob/master/en-eng/WebUserInterfaceNavigation.md](https://github.com/pipiche38/Domoticz-Zigate-Wiki/blob/master/en-eng/WebUserInterfaceNavigation.md)

After adding a child zigbee device, it will appear in the domoticz interface.
