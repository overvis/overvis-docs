# OPCB-221 Applications

## 1. Modbus RTU/ASCII to Modbus TCP converter

- Local access.
- Remote access via internet.

![Diagram of using the OPCB-221 as a Modbus RTU/ASCII to Modbus TCP converter](./images/opcb.control.flow.1.png)

This variant is usually specified with:

- 1...2 RS-485 ports enabled in Master mode;
- Modbus TCP server enabled;
- Modbus TCP clients turned off.

The RS-485 port or ports are configured for communication with the connected devices (speed and
byte format, i.e. parity and number of stop bits are specified). The gateway for RS-485 is
configured for the desired Modbus address ranges.

## 2. Converter for remote access to Modbus TCP via Internet

![OPCB-221 application diagram as a converter for remote Modbus TCP access via the Internet](./images/opcb.control.flow.2.png)

This option is usually specified with:

- OPCB-221 access to the Internet;
- 1...2 Modbus TCP clients enabled;
- Modbus TCP server enabled;
- disabled RS-485.

The Modbus TCP clients are configured to communicate with the desired remote servers or devices (IP
addresses are specified). The gateway for Modbus TCP clients is configured to the desired Modbus
address ranges.

## 3. Converter for remote access via a cloud server

![OPCB-221 application diagram as a converter for remote access via a cloud server](./images/opcb.control.flow.3.png)

The variant is most often a cloud variant of any of the other modes, or a combination of them.
However, when accessed exclusively through a cloud server, other ports for local control devices
(such as a Modbus TCP server) are most often disabled. The option is used under the following
conditions:

- The OPCB-221 has access to the Internet;
- reverse (device-to-server) connection to the cloud server is enabled.

To access OPCB-221 via the cloud server, you just need to provide access OPCB-221 to the Internet
and make sure that the address of the desired server is specified in the settings.

It is not recommended to disable the reverse (device-to-server) connection, even if remote user
access is not required at this time.

## 4. Modbus TCP to Modbus RTU/ASCII converter

- connection of Modbus TCP devices to a master that has an RS-485 interface
- extension, extension or synchronization of the RS-485 by means of converter(s) OPCB-221

![Diagram of using the OPCB-221 as a Modbus TCP to Modbus RTU/ASCII converter](./images/opcb.control.flow.4.png)

This variant is usually specified with:

- 1...2 RS-485 ports enabled in Slave mode;
- 1...2 Modbus TCP clients enabled;
- Modbus TCP server turned off;
- disabled RS-485.

The RS-485 port or ports are configured for communication with the connected master device or
devices connected (the speed and byte format, i.e. parity and number of stop bit). Modbus TCP
clients are configured to communicate with the desired remote servers or devices (IP addresses are
specified). Gateway for Modbus TCP clients is configured to the desired Modbus address ranges.

## Combinations

![OPCB-221 all combinations diagram](./images/opcb.control.flow.5.png)

The number of combined variants is quite large, basically they contain different parts of the
already listed variants.

For example:

- one RS-485 can be configured as either Master or Slave, but not both both at the same time.
  Accordingly, depending on this, either the slaves devices from variant 1, or control devices from
  variant 2 may or may not present or absent in either mode;
- the same applies to the second RS-485, which is independent of the first. Therefore, a
  combination of Master and Slave (a mixture of variants 1 and 4) is acceptable;
- Modbus TCP devices (from variants 2 and 4) can be present or not TCP devices (variants 2 and 4)
  can be present or absent in any of the modes, depending on the settings of the Modbus TCP;
- Modbus TCP control devices (of variants 1 and 2) may or may not be present in any mode, depending
  on the settings of the Modbus TCP Modbus TCP control devices (variants 1 and 2) may or may not be
  present in any of the modes depending on the local Modbus TCP Modbus TCP server settings;
- cloud server (from option 3) may or may not be present in any of modes, depending on the settings
  of the return connection.

# Quick start

## Wiring the OPCB-221

1. prepare RS485 bus, LAN / Wi-Fi router connected to Internet;

![prepared items](./images/start11.png) ![](./images/Wiring_the_OPCB-221_1.svg)

2. unpack OPCB-221;

![package contents](./images/start12.png) ![](./images/Wiring_the_OPCB-221_2.svg)

- compare OPCB-221 package contents with the manual inside, and ensure there are no traces of
  damage to any of the items;
- **compare the power unit characteristics with the local mains**;
- keep the device in the target environment for 2 hours (at least) before powering it;

3. Memory card is factory pre-installed into the OPCB-221 `Memory` slot;

![memory card](./images/start13.png) ![](./images/Wiring_the_OPCB-221_3.svg)

4. connect RS-485 bus to RS485-to-USB convertor module (included);

![RS-485 bus](./images/start14.png) ![](./images/Wiring_the_OPCB-221_4.svg)

- note the direct signal contact `A`/`A+`/`D+` and inverted signal contact `B`/`B-`/`D-`
  connection, the bus shield (if any) should be grounded at a single point close to the terminal;
- if the module is the last (or the first) connection on the bus line, consider adding a 120Ohm
  0,25W termination resistor in parallel to improve performance for high-speed or long lines;

5. connect other Modbus controllable devices to RS-485 bus;

![other RS-485 devices](./images/start15.png) ![](./images/Wiring_the_OPCB-221_5.svg)

- the number of the devices sharing the same bus (without RS-485 repeater device insertion, not
  included) is limited by their receiver current consumption, usually at least 32 devices with 1mA
  consumption can be connected (more if their consumption is less than 1mA);
- each device should be added on either of the two ends of the bus (instead of the termination
  resistor), avoiding junctions and branches;
- the direct and inverted contacts should be connected accordingly, otherwise some or all of the
  bus devices would be inaccessible;
- if the bus contacts order is unknown for a custom device, it is safe to test it later (by trying
  it the both ways);
- the bus shield part between any two devices should be grounded separately instead of connecting
  it to the other shield parts;
- consider adding a 120Ohm 0,25W termination resistor in parallel to the terminal contacts of the
  uttermost device (that is, two resistors on the both ends of the RS-485 bus) to improve
  performance for high-speed or long lines;

6. connect RS-485-to-USB convertor module to OPCB-221;

![RS-485-to-USB module](./images/start16.png) ![](./images/Wiring_the_OPCB-221_6.svg)

- any of the OPCB-221 USB jacks marked `Ext.` can be used;
- consider fixing the module in place with the zip ties (included) to avoid accidental
  disconnection;

7. optional: if using Ethernet instead of Wi-Fi, connect OPCB-221 to the router with the LAN cable
   (included);

![Ethernet cable](./images/start17.png) ![](./images/Wiring_the_OPCB-221_7.svg)

- do not connect to the router jack `WAN`/`Internet`, but use the conventional `LAN` jack instead;

8. connect power unit (included) to OPCB-221 `Power` jack;

![power unit](./images/start18.png) ![](./images/Wiring_the_OPCB-221_8.svg)

9. connect power unit to the mains, power on OPCB-221 and other RS-485 bus devices.

![power on](./images/start19.png) ![](./images/Wiring_the_OPCB-221_9.svg)

- **ensure the mains connection is portected (with the 1A current limiter)**;

## Getting OPCB-221 to Internet

1. use your telephone or another Wi-Fi smart device to scan for access point `OPCB_XXXXXX` to
   appear (where XXXXXX are the last 6 digits in the labelled device MAC address, the startup can
   take up to 1 minute);

![OPCB access point in list](./images/start21.png)

2. connect to the access point;

![OPCB access point connected](./images/start22.png)

- the default password is `12345678`;

3. follow the `setup.overvis.com` link to open OPCB-221 setup page;

![OPCB page](./images/start23.png)

- if the warning page opens with misdirection explanation, try disconnecting your smart device from
  the Internet first;
- the direct address `192.168.4.1` can also be used to avoid misdirections;

4. setup OPCB-221 network interfaces to match your router configuration;

![OPCB Wi-Fi setup page](./images/start24.png)

- use ELAN (Ethernet) or WLAN (Wi-Fi) interface page, or both;
- to use Wi-Fi, switch off access point mode;
- save the settings after completing each page;
- contact your local network administrator to determine the interfaces correct configuration;

5. optional: setup RS485 for baudrate and exchange format of the RS485 bus (this can be done
   later);

![OPCB RS-485 setup page](./images/start25.png)

- all the bus devices should share the same baudrate and format;
- save the settings after completing each page;

6. reset OPCB-221 for the changes to take effect.

![OPCB reboot prompt](./images/start26.png)

## Using Overvis to access OPCB-221

1. follow the labelled link to get to the cloud server;

- scan the labeled QR code to follow the link;
- or open the browser on your smart device, then type in the link printed on the label;

2. if not logged in, authorize at the cloud server;

![Overvis authorization](./images/start32.png)

- if password forgotten, restore your password by email with an option at the server login page;
- if no account yet, register a new account with an option at the server login page;

3. on a new Modbus network settings page, type in the new network name in the "Name" field;

![Overvis new Modbus network configuration](./images/start33.png)

- ensure the connection is established, and the device version is displayed at the top of the
  Modbus network settings page;

4. optional: add devices connected to the OPCB-221 (via Modbus RTU/ASCII or Modbus TCP) to the
   "Connected devices" list (this can be done later);

5. press the "Install" button;

6. finally test the connection by pressing any OPCB-221 parameter value in the table below;

![Overvis test parameter readout](./images/start36.png)

- `...` is displayed in the values column if no value is known yet, otherwise a value (e.g. a
  number) is displayed;
- the value would then update continuously (until it is pressed again).
