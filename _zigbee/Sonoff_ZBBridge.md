---
date_added: 2020-08-21
model: ZBBridge
vendor: Itead
title: Sonoff Zigbee Bridge
category: coordinator
supports: coordinator
zigbeemodel: ['']
compatible: [tasmota,zha]
mlink: https://www.itead.cc/sonoff-zbbridge.html
link: https://www.itead.cc/sonoff-zbbridge.html
link2: https://www.aliexpress.com/af/sonoff-zbbridge.html
link3: https://www.banggood.com/SONOFF-ZBBridge-Smart-Bridge-p-1674754.html
---
<h3>Support for this device is experimental!!!</h3>

## Flash ESP8266

Remove the 4 rubber feet to access screws and disassemble the device. Take out the PCB and connect breadboard cables to labelled pin holes (if you only have Dupont wires you will need to solder them since the holes are too small for them).

![ZBBridge pinout](/assets/images/sonoff_ZBBridge_pinout.jpg)

|ZbBridge|Adapter
|---     |--- 
|ETX     | RX
|ERX     | TX
|IO0     | GND
|GND     | GND
|3v3     | 3V3/VCC

Download [tasmota-zbbridge](https://github.com/arendst/Tasmota/blob/firmware/firmware/tasmota/tasmota-zbbridge.bin?raw=true), a unique binary built specifically for ZBBridge and flash it using your favorite flashing software.

When the ZbBridge is flashed with Tasmota, **disconnect all breadboard wires** and power the board using its USB port with a 5V 1A power supply. Configure Wi-Fi [over Tasmota AP](https://tasmota.github.io/docs/Getting-Started/#using-web-ui) (you cannot configure the device over serial with this binary). After it is connected to your network access the webUI again. 

## Flash Zigbee module

Download Zigbee module firmware [`ncp-uart-sw_6.7.6_115200.ota`](https://github.com/arendst/Tasmota/blob/development/tools/fw_zbbridge/ncp-uart-sw_6.7.6_115200.ota?raw=true) from Tasmota GitHub located in [`Tasmota/tools/fw_zbbridge/`](https://github.com/arendst/Tasmota/blob/development/tools/fw_zbbridge).

Go to **Firmware Upgrade** and next to "Upgrade by file upload" use the _Choose File_ button and select Zigbee module firmware you downloaded (`ncp-uart-sw_6.7.6_115200.ota`). 

![ZBBridge Zigbee module flash](/assets/images/sonoff_ZBBridge_ota.jpg)

Click on **Start upgrade**, be patient and wait for a few minutes until flashing is complete. Once it is done, Tasmota will reboot. If the flash was successful the console will show Zigbee2Tasmota starting:

```json
16:31:11 ZIG: Resetting EZSP device
16:31:12 RSL: tele/zbbridge/RESULT = {"ZbState":{"Status":1,"Message":"EFR32 booted","RestartReason":"Power-on","Code":2}}
16:31:12 RSL: tele/zbbridge/RESULT = {"ZbState":{"Status":55,"Version":"6.7.6.0","Protocol":8,"Stack":2}}
16:31:12 RSL: tele/zbbridge/RESULT = {"ZbState":{"Status":3,"Message":"Configured, starting coordinator"}}
16:31:13 RSL: tele/zbbridge/RESULT = {"ZbState":{"Status":56,"IEEEAddr":"0x80E423FFFE225691","ShortAddr":"0x0000","DeviceType":1}}
16:31:14 ZIG: Subscribe to group 0 'ZbListen0 0'
16:31:14 RSL: tele/zbbridge/RESULT = {"ZbState":{"Status":0,"Message":"Started"}}
16:31:14 ZIG: Zigbee started
16:31:14 ZIG: No zigbee devices data in Flash
```

Follow further instructions depending on your chosen method.

## For Tasmota
You can start pairing Zigbee devices with `ZbPermitJoin 1` command.

Read [Zigbee](http://tasmota.github.io/docs/Zigbee) documentation for complete guide to pairing and managing your devices.

## For Home Assistant ZHA
After Zigbee firmware is flashed apply the template

```json
{"NAME":"ZHA ZBBridge","GPIO":[56,208,0,209,59,58,0,0,0,0,0,0,17],"FLAG":0,"BASE":18}
```

Create a rule in Tasmota to start TCPBridge on boot
```console
Rule1 ON System#Boot do TCPStart 8888 endon
```

> You can change `8888` to a port you prefer.

Enable rule with `Rule1 1` and restart ZbBridge.

In Home Assistant (requires version 0.113+) go to **Configuration - Integrations**, click the **+** icon, search for ZHA integration and select it. 

[![ZBBridge ZHA Configuration](/assets/images/sonoff_ZBBridge_zha.jpg)]((/assets/images/sonoff_ZBBridge_zha.jpg))

1. choose "Enter Manually" for serial port
2. for Radio Type choose "EZSP" 
3. under Serial device path enter `socket://[zbbridge_ip]:8888` replacing `[zbbridge_ip]` with its IP address. Do not use hostnames. 
   - if you changed the port number use yours
   - set port speed to "115200"
4. when the ZbBridge is discovered you will get a confirmation message

[![ZBBridge ZHA Configuration](/assets/images/sonoff_ZBBridge_zha2.jpg)]((/assets/images/sonoff_ZBBridge_zha2.jpg))