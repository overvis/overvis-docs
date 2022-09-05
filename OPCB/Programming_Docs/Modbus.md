## MODBUS Operation Manual

Depending on the settings, the OPCB-221 can receive and process MODBUS requests
over multiple ports, including:

- MODBUS TCP requests from the cloud server via a return connection to the cloud
  server;
- MODBUS TCP requests from one or more clients (but not more than the number of
  clients indicated in the specifications) through the MODBUS TCP port (for
  which the OPCB-221 acts as a MODBUS TCP server);
- MODBUS RTU or MODBUS ASCII requests from a master device or a device via one
  or two serial ports (in this case the OPCB-221 acts as a slave device on the
  serial port).

The address (identifier) of the device in the request determines the direction
of the request for its processing and response.

If the request is addressed to another device then depending on the gateway
settings, it can be transmitted (with protocol conversion where required), to
one of the specified directions, including:

- to one of the two serial ports by means of MODBUS RTU or MODBUS ASCII request
  (for devices on these ports the OPCB-221 acts as the master);
- to one of the two remote servers as a MODBUS TCP request (for which OPCB-221
  acts as a MODBUS TCP client).

If a reply is received back in the correct format, it is returned to the
requesting party. If the response waiting time has elapsed or the format of the
response is incorrect, the OPCB-221 generates and transmits to the client the
appropriate MODBUS exception code.

If the request is addressed to an internal OPCB-221 virtual device, it is not
transmitted, but processed by the device itself, and the response is returned to
the requesting party. The lists of parameters available in the MODBUS registers
are shown below.

- the device supports read and/or write function requests to the storage
  registers;
- the listed register addresses correspond directly to the values of the address
  field in MODBUS requests;
- for read-only parameters, the range of valid values is not specified;
- for parameters available for reading or writing, the range of valid values is
  specified;
- digital parameters that take up more than one register store bytes by two in
  each register, the highest one first;
- parameters of the _character string_ type store string character codes, one in
  each register, from left to right; if the string occupies less registers than
  specified in the address range, the string ends with the code 0; if the
  initial value is not specified, it equals an empty string;
- parameters of the _MODBUS route_ type store route ranges - MODBUS addresses
  pairs (the first range address and the last range address), one address in
  each register; if any of the route ranges is not used, both addresses equal 0;
- parameters like _IPv4 address_ and _MAC-48 address_ store address bytes one in
  each register, from left to right.

### Group of identification parameters

| Parameter   | Description                                      | Address(es) |
| ----------- | ------------------------------------------------ | ----------- |
| Device type | Manufacturer identification code (42 - OPCB-221) | 0           |
| Version     | Version of MODBUS communication software         | 1           |
| Build       | Version build of MODBUS communication software   | 2           |

### Group of control parameters

| Parameter | Permissible values | Initial value | Description              | Address(es) |
| --------- | ------------------ | ------------- | ------------------------ | ----------- |
| Command   | 0...65535          | 0             | Command code (see below) | 50          |

#### Available command codes (register 50):

- `3034` – request the cloud server to cancel the activation (unlink the device
  account) and a new activation code;
- `3036` – disconnect from the cloud server (if connected) and reconnect (if the
  connection is allowed);
- `43943` – soft reboot the device;
- `43947` – restart the MODBUS communication software;
- `52271` – write configuration changes to the configuration file;
- `52719` – reset the configuration changes to the factory defaults and write
  them to the configuration file;
- `53036` – cancel the configuration changes and load the settings from the
  configuration file.

### Group of software setup parameters

#### WebAPI server

This supplementary server provides WebAPI and is also used by the Web user
interface to manage the device.

**Changing these parameters is not recommended.**

| Parameter                                                           | Permissible values  | Factory default | Description                                                                                                                                                                      | Address(es) |
| ------------------------------------------------------------------- | ------------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| Interface for the WebAPI server                                     | 0 / 1 / 2 / 3 / 255 | 1               | 0 – disabled;<br>1 – on the local Loopback;<br>2 – on Ethernet;<br>3 – on Wi-Fi;<br>255 – on any available                                                                       | 200         |
| WebAPI server port                                                  | 0...65535           | 8502            |                                                                                                                                                                                  | 201         |
| Web server port reopen delay, s                                     | 1...600             | 5               |                                                                                                                                                                                  | 202         |
| Idle time before Web client disconnection, s                        | 15...3600           | 15              |                                                                                                                                                                                  | 203         |
| Timeout of response to the Modbus requests sent through Web API, ms | 1...60000           | 8000            |                                                                                                                                                                                  | 205         |
| Maximum number of connections to the server                         | 1...20              | 4               | Maximum number of simultaneously connected Web clients. If this number is exceeded, HTML errors and empty request responses may be generated, or new clients may be disconnected | 207         |
| Period between data reception by the Web server, µs                 | 1...65535           | 5000            | Sleep time when this port is inactive. Determines the processor load<br>1 – minimum packet delay, maximum load                                                                   | 208         |

#### Reverse connection to the cloud server

Connection to the predefined address is established and maintained to accept
MODBUS TCP requests. This allows remote control of the device (and the devices
connected to it) by the access rights issued by the cloud server. In case the
device is not activated on the cloud server (to use remote management), the
connection remains on standby until activated.

It is not recommended to disable this connection even if remote control is not
required by the user.

| Parameter                                                  | Permissible values  | Factory default      | Description                                                                                                                                                                                                                          | Address(es) |
| ---------------------------------------------------------- | ------------------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------- |
| Interface for connection                                   | 0 / 1 / 2 / 3 / 255 | 255                  | 0 – disabled;<br>1 – via local Loopback;<br>2 – via Ethernet;<br>3 – via Wi-Fi;<br>255 – via any available                                                                                                                           | 250         |
| Cloud server port                                          | 1...65535           | 20502                |                                                                                                                                                                                                                                      | 251         |
| Reconnection to the cloud server delay, s                  | 1...600             | 5                    |                                                                                                                                                                                                                                      | 252         |
| Idle time before disconnecting from the cloud server, s    | 60...3600           | 90                   |                                                                                                                                                                                                                                      | 253         |
| Send identification data on connection to the cloud server | 0 / 1               | 1                    |                                                                                                                                                                                                                                      | 254         |
| Timeout of response to the cloud server, ms                | 1...60000           | 1500                 | If there is no response for longer than the set time, a MODBUS TCP exception is generated and sent instead of a response                                                                                                             | 255         |
| Maximum number of requests sent by the cloud server        | 1...20              | 8                    | The parameter is used to send requests and/or responses simultaneously (allows to optimize traffic, especially for wireless or mobile connections) 1 - requests are always delayed by the client before processing previous requests | 256         |
| Cloud server address                                       | Character string    | `modbus.overvis.com` | Host name or IP address of the cloud server                                                                                                                                                                                          | 286...349   |

#### MODBUS gateway

When any MODBUS requests are received, each request passes the internal MODBUS
gateway and is forwarded to the port with the fitting route. If several routes
match, the requst is forwarded to each of them, and the first of the responses
to come is accepted. The route is defined by a set of MODBUS addresses ranges,
except internal virtual device (which has a single address).

| Parameter                                  | Permissible values | Factory default | Description                                                                                                                        | Address(es) |
| ------------------------------------------ | ------------------ | --------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| Route to virtual device                    | 0 / 1              | 1               | 0 – internal virtual device is not available;<br>1 – internal virtual device is available                                          | 350         |
| Virtual device address                     | 0...255            | 111             | Requests to this address are handled by the internal virtual device                                                                | 351         |
| The route to the serial port #1            | MODBUS route       | `1-255`         | Requests for addresses in this route ranges are passed through serial port #1. Zero range bounds disable the range                 | 354...365   |
| The route to the serial port #2            | MODBUS route       | `0-0`           | Requests for addresses in this route ranges are passed through serial port #2. Zero range bounds disable the range                 | 366...377   |
| The route to the serial port #3            | MODBUS route       | `0-0`           | Requests for addresses in this route ranges are passed through serial port #3. Zero range bounds disable the range                 | 378...389   |
| The route to the serial port #4            | MODBUS route       | `0-0`           | Requests for addresses in this route ranges are passed through serial port #4. Zero range bounds disable the range                 | 390...401   |
| The route to the client (remote server) #1 | MODBUS route       | `1-255`         | Requests for addresses in this route ranges are passed through client (remote server) port #1. Zero range bounds disable the range | 402...413   |
| The route to the client (remote server) #2 | MODBUS route       | `0-0`           | Requests for addresses in this route ranges are passed through client (remote server) port #2. Zero range bounds disable the range | 414...425   |
| The route to the client (remote server) #3 | MODBUS route       | `0-0`           | Requests for addresses in this route ranges are passed through client (remote server) port #3. Zero range bounds disable the range | 426...437   |
| The route to the client (remote server) #4 | MODBUS route       | `0-0`           | Requests for addresses in this route ranges are passed through client (remote server) port #4. Zero range bounds disable the range | 438...449   |

#### MODBUS TCP server

The server opens a port and accepts remote MODBUS TCP client connections. The
devices connected to the product become available to the clients of this server.
Connected clients can send requests to the product (addressing internal virtual
device) or devices connected to it.

| Parameter                                                     | Permissible values  | Factory default | Description                                                                                                                                                                                                                                                                                                                                             | Address(es) |
| ------------------------------------------------------------- | ------------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| Interface for MODBUS TCP server                               | 0 / 1 / 2 / 3 / 255 | 255             | 0 – disabled;<br>1 – on the local Loopback;<br>2 – on Ethernet;<br>3 – on Wi-Fi;<br>255 – on any available                                                                                                                                                                                                                                              | 450         |
| MODBUS TCP server port                                        | 1...65535           | 502             |                                                                                                                                                                                                                                                                                                                                                         | 451         |
| MODBUS TCP server port reopen delay, s                        | 1...600             | 5               |                                                                                                                                                                                                                                                                                                                                                         | 452         |
| Idle time before remote MODBUS TCP client disconnection, s    | 15...3600           | 60              |                                                                                                                                                                                                                                                                                                                                                         | 453         |
| Timeout of response to a remote MODBUS TCP client, ms         | 1...60000           | 1500            | If there is no response for longer than the set time, a MODBUS TCP exception is generated and sent instead of a response                                                                                                                                                                                                                                | 455         |
| Maximum number of requests sent by a remote MODBUS TCP client | 1...20              | 1               | The parameter is used to send requests and/or responses simultaneously (allows to optimize traffic, the remote client can send several requests simultaneously and must process several responses simultaneously, or have an incoming buffer of sufficient size)<br>1 – requests are always delayed by the client until previous requests are processed | 456         |
| Maximum number of connections to the server                   | 1...20              | 4               | Maximum number of simultaneously connected remote MODBUS TCP clients. If this number is exceeded, it is possible to generate exceptions in response to requests, or disconnect new clients                                                                                                                                                              | 457         |
| Period between data reception via MODBUS TCP port, µs         | 1...65535           | 2000            | Sleep time when this port is inactive\*. Determines the processor load<br>1 – minimum packet delay, maximum load                                                                                                                                                                                                                                        | 458         |

#### Serial port #1

In Master mode, the device acts as a MODBUS master on this port. Other devices
on this port are considered slave devices _connected to_ the product. The port
opens as one of the destinations to which requests passing through the MODBUS
gateway can be sent. Received responses are returned to the party from which the
request came.

In Slave mode, the device acts as a MODBUS slave on this port. Devices connected
on other directions become available to the MODBUS master on this port. The port
opens to receive requests to the product or devices connected to it.

| Parameter                      | Permissible values                                                                     | Factory default | Description                                                                                                                                                                                                            | Address(es) |
| ------------------------------ | -------------------------------------------------------------------------------------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| Speed, b/s                     | 300 / 600 / 1200 / 1800 / 2400 / 4800 / 9600 / 19200 / 38400 / 57600 / 115200 / 230400 | 9600            |                                                                                                                                                                                                                        | 500...501   |
| Byte format                    | 1 / 2 / 3 / 4                                                                          | 3               | 1 – no parity bit, 1 stop bit;<br>2 – no parity bit, 2 stop bits, or parity bit installed, 1 stop bit (MARK);<br>3 – parity bit with even-check, 1 stop bit (EVEN);<br>4 – parity bit with odd-check, 1 stop bit (ODD) | 502         |
| MODBUS frame format            | 0 / 1 / 2 / 3                                                                          | 0               | 0 – MODBUS RTU Master;<br>1 – MODBUS ASCII Master;<br>2 – MODBUS RTU Slave;<br>3 – MODBUS ASCII Slave                                                                                                                  | 503         |
| MODBUS response start time, ms | 1...60000                                                                              | 1000            |                                                                                                                                                                                                                        | 504         |
| Serial port interface          | Character string                                                                       | `ttyUSB0`       | \*\*                                                                                                                                                                                                                   | 530...549   |

#### Serial port #2

This port acts and is configured in a way similar to the port #1, except for
configuration registers addresses 550...599.

#### Serial port #3

This port acts and is configured in a way similar to the port #1, except for
configuration registers addresses 600...649.

#### Serial port #4

This port acts and is configured in a way similar to the port #1, except for
configuration registers addresses 650...699.

#### MODBUS TCP client #1

Connection to a given address is established and maintained as one of the
directions to which requests passing through the MODBUS gateway can be sent.
Received responses are returned to the party from which the request came. Other
devices accessible through the remote server are considered slave devices
_connected to_ the product.

| Parameter                                                         | Permissible values  | Factory default | Description                                                                                                                                                                                                                                                                                                                                        | Address(es) |
| ----------------------------------------------------------------- | ------------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| Interface for connecting the client to a remote MODBUS TCP server | 0 / 1 / 2 / 3 / 255 | 255             | 0 – not used;<br>1 – via local Loopback;<br>2 – via Ethernet;<br>3 – via Wi-Fi;<br>255 – via any available                                                                                                                                                                                                                                         | 700         |
| Remote MODBUS TCP server port                                     | 1...65535           | 502             | \*\*\*                                                                                                                                                                                                                                                                                                                                             | 701         |
| Reconnection to the remote server delay, s                        | 1...600             | 5               |                                                                                                                                                                                                                                                                                                                                                    | 702         |
| Timeout of response from the remote server, ms                    | 1...60000           | 8000            | If no answer is received for the given period, the waiting period stops and the client generates and returns an error                                                                                                                                                                                                                              | 705         |
| Maximum number of requests processed by the remote server         | 1...20              | 1               | Parameter is used to send requests and / or responses simultaneously (allows to optimize traffic, the server can send multiple responses simultaneously and must handle multiple requests simultaneously or have an incoming buffer of sufficient capacity)<br>1 – requests are always delayed by the client until previous requests are processed | 706         |
| Remote MODBUS TCP server address                                  | IPv4 address        | `192.168.1.121` | \*\*\*                                                                                                                                                                                                                                                                                                                                             | 707...710   |

#### MODBUS TCP client #2

This client acts and is configured in a way similar to the client #1, except for
configuration registers addresses 750...799.

#### MODBUS TCP client #3

This client acts and is configured in a way similar to the client #1, except for
configuration registers addresses 800...849.

#### MODBUS TCP client #4

This client acts and is configured in a way similar to the client #1, except for
configuration registers addresses 850...899.

#### Miscellaneous

| Parameter | Permissible values | Factory default | Description | Address(es) |
| --------- | ------------------ | --------------- | ----------- | ----------- |
| Host Name | Character string   | `OPCB-221`      |             | 900...963   |

#### Notes

\* – parameter is used for all MODBUS TCP compatible ports (MODBUS TCP server,
MODBUS TCP clients, reverse cloud server connection)

\*\* – parameter value must not be repeated for the other serial ports

\*\*\* – combination of address and port parameters must not be repeated for the
other MODBUS TCP clients; it should also neither equal the product own addresses
nor lead to them, to avoid logical rings

### Group of identification parameters for the reverse connection to the cloud server

Parameters are used by the cloud server to identify the product among the
reversely connecting devices.

| Parameter                | Description                                                                                                                                                                                                                                                        | Address(es)   |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------- |
| Type of device           | Manufacturer identification code (42 - OPCB-221)                                                                                                                                                                                                                   | 1000          |
| Version                  | Version of MODBUS communication software                                                                                                                                                                                                                           | 1001          |
| Unique MAC address       | Globally unique MAC-48 address, which is used for identification                                                                                                                                                                                                   | 1002 ... 1007 |
| Reverse connection flags | Bit 0 - the MAC address (reg. 1002..1007) uniqueness confirmation.<br>Bit 1 - the activation code is set.<br>Bit 2 - the device requests a new activation code (e.g., after a local command to deactivate the reverse connection).<br>Bit 3 - connection activated | 1008          |

### Group of parameters to manage the reverse connection to the cloud server

Parameters are used by the cloud server to control the reconnection.

| Parameter                           | Permissible values                          | Description                                                                                                                                                                                                                                                                                                                                                        | Address(es)   |
| ----------------------------------- | ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------- |
| Activation code on the cloud server | 0 / 10 000 000 ... 99 999 999 / 100 000 000 | 8-digit code that is transmitted by the cloud server when reconnecting and can be read locally by the user. When the user enters the code on the cloud server, this reverse connection is activated (transmitted to the given user on the server).<br>0 – connection activated;<br>10 000 000 ... 99 999 999 – activation code;<br>100 000 000 – code not assigned | 1009 ... 1010 |

### Group of state parameters for the reverse connection to the cloud server

| Parameter                                     | Description                                                                                                         | Address(es) |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | ----------- |
| Interface used to connect to the cloud server | 0 – not connected;<br>1 – via local Loopback;<br>2 – via Ethernet;<br>3 – via Wi-Fi;<br>255 – via another interface | 1111        |
| Time since last connection, s                 |                                                                                                                     | 1112        |
| Connection attempts count                     |                                                                                                                     | 1114        |
| Last error code                               | 0 - no error                                                                                                        | 1116        |

### Group of state parameters

#### Product state

| Parameter                                    | Description | Address(es) |
| -------------------------------------------- | ----------- | ----------- |
| Time since OS restart, s                     |             | 1304...1305 |
| Core power voltage, mV                       |             | 1308        |
| Core temperature, Celsium scale centidegrees |             | 1309        |
| Total memory size, MB                        |             | 1310...1311 |
| Used memory size, MB                         |             | 1312...1313 |
| Total memory card size, MB                   |             | 1314...1315 |
| Used memory card size, MB                    |             | 1316...1317 |
| Number of MODBUS TCP server remote clients   |             | 1318        |

#### Ethernet state

| Parameter                                    | Description                                                                                      | Address(es) |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------ | ----------- |
| Current IP address of the Ethernet interface |                                                                                                  | 1320...1323 |
| Net prefix                                   |                                                                                                  | 1324        |
| Ethernet interface status                    | 0 - unavailable;<br>1 - no address;<br>2 - no connection to gateway;<br>3 - connected to gateway | 1325        |
| Gateway IP address of the Ethernet interface |                                                                                                  | 1326...1329 |
| Gateway ping, ms                             |                                                                                                  | 1330        |

#### Wi-Fi state

| Parameter                                 | Description                                                                                      | Address(es) |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------ | ----------- |
| Current IP address of the Wi-Fi interface |                                                                                                  | 1332...1335 |
| Net prefix                                |                                                                                                  | 1336        |
| Wi-Fi interface status                    | 0 - unavailable;<br>1 - no address;<br>2 - no connection to gateway;<br>3 - connected to gateway | 1337        |
| Gateway IP address of the Wi-Fi interface |                                                                                                  | 1338...1341 |
| Gateway ping, ms                          |                                                                                                  | 1342        |

#### Internet state

| Parameter                   | Description                                      | Address(es) |
| --------------------------- | ------------------------------------------------ | ----------- |
| Internet status             | 2 - connection absent;<br>3 - connection present | 1345        |
| Internet node #1 ping, ms   |                                                  | 1346        |
| Internet node #2 ping, ms   |                                                  | 1347        |
| Internet node #1 IP address |                                                  | 1348...1351 |
| Internet node #2 IP address |                                                  | 1352...1355 |

#### MODBUS TCP client #1 state

| Parameter                     | Description                                                                                                         | Address(es) |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------- | ----------- |
| Interface used to connect     | 0 – not connected;<br>1 – via local Loopback;<br>2 – via Ethernet;<br>3 – via Wi-Fi;<br>255 – via another interface | 1360        |
| Connection attempts count     |                                                                                                                     | 1361        |
| Time since last connection, s |                                                                                                                     | 1362        |
| Last error code               | 0 - no error                                                                                                        | 1364        |

#### MODBUS TCP client #2 state

| Parameter                     | Description                                                                                                         | Address(es) |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------- | ----------- |
| Interface used to connect     | 0 – not connected;<br>1 – via local Loopback;<br>2 – via Ethernet;<br>3 – via Wi-Fi;<br>255 – via another interface | 1366        |
| Connection attempts count     |                                                                                                                     | 1367        |
| Time since last connection, s |                                                                                                                     | 1368        |
| Last error code               | 0 - no error                                                                                                        | 1370        |

#### MODBUS TCP client #3 state

| Parameter                     | Description                                                                                                         | Address(es) |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------- | ----------- |
| Interface used to connect     | 0 – not connected;<br>1 – via local Loopback;<br>2 – via Ethernet;<br>3 – via Wi-Fi;<br>255 – via another interface | 1372        |
| Connection attempts count     |                                                                                                                     | 1373        |
| Time since last connection, s |                                                                                                                     | 1374        |
| Last error code               | 0 - no error                                                                                                        | 1376        |

#### MODBUS TCP client #4 state

| Parameter                     | Description                                                                                                         | Address(es) |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------- | ----------- |
| Interface used to connect     | 0 – not connected;<br>1 – via local Loopback;<br>2 – via Ethernet;<br>3 – via Wi-Fi;<br>255 – via another interface | 1378        |
| Connection attempts count     |                                                                                                                     | 1379        |
| Time since last connection, s |                                                                                                                     | 1380        |
| Last error code               | 0 - no error                                                                                                        | 1382        |

#### Serial port #1 state

| Parameter                | Description                                                                                                                                    | Address(es) |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| Status                   | 0 – disabled;<br>1 – opening port;<br>2 – started RTU Master;<br>3 - started ASCII Master<br>4 – started RTU Slave;<br>5 – started ASCII Slave | 1384        |
| Port open attempts count |                                                                                                                                                | 1385        |
| Last error code          | 0 - no error                                                                                                                                   | 1386        |

#### Serial port #2 state

| Parameter                | Description                                                                                                                                    | Address(es) |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| Status                   | 0 – disabled;<br>1 – opening port;<br>2 – started RTU Master;<br>3 - started ASCII Master<br>4 – started RTU Slave;<br>5 – started ASCII Slave | 1388        |
| Port open attempts count |                                                                                                                                                | 1389        |
| Last error code          | 0 - no error                                                                                                                                   | 1390        |

#### Serial port #3 state

| Parameter                | Description                                                                                                                                    | Address(es) |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| Status                   | 0 – disabled;<br>1 – opening port;<br>2 – started RTU Master;<br>3 - started ASCII Master<br>4 – started RTU Slave;<br>5 – started ASCII Slave | 1392        |
| Port open attempts count |                                                                                                                                                | 1393        |
| Last error code          | 0 - no error                                                                                                                                   | 1394        |

#### Serial port #4 state

| Parameter                | Description                                                                                                                                    | Address(es) |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| Status                   | 0 – disabled;<br>1 – opening port;<br>2 – started RTU Master;<br>3 - started ASCII Master<br>4 – started RTU Slave;<br>5 – started ASCII Slave | 1396        |
| Port open attempts count |                                                                                                                                                | 1397        |
| Last error code          | 0 - no error                                                                                                                                   | 1398        |

### Group of statistics parameters

#### Reverse connection to the cloud server statistics

| Parameter                                            | Description | Address(es) |
| ---------------------------------------------------- | ----------- | ----------- |
| Average receive speed, B/min                         |             | 1400        |
| Average transmit speed, B/min                        |             | 1401        |
| Number of received bytes since last connection, B    |             | 1402        |
| Number of transmitted bytes since last connection, B |             | 1404        |
| Number of received bytes since restart, B            |             | 1406        |
| Number of transmitted bytes since restart, B         |             | 1408        |

#### MODBUS TCP client #1 statistics

| Parameter                                            | Description | Address(es) |
| ---------------------------------------------------- | ----------- | ----------- |
| Average receive speed, B/min                         |             | 1410        |
| Average transmit speed, B/min                        |             | 1411        |
| Number of received bytes since last connection, B    |             | 1412        |
| Number of transmitted bytes since last connection, B |             | 1414        |
| Number of received bytes since restart, B            |             | 1416        |
| Number of transmitted bytes since restart, B         |             | 1418        |

#### MODBUS TCP client #2 statistics

| Parameter                                            | Description | Address(es) |
| ---------------------------------------------------- | ----------- | ----------- |
| Average receive speed, B/min                         |             | 1420        |
| Average transmit speed, B/min                        |             | 1421        |
| Number of received bytes since last connection, B    |             | 1422        |
| Number of transmitted bytes since last connection, B |             | 1424        |
| Number of received bytes since restart, B            |             | 1426        |
| Number of transmitted bytes since restart, B         |             | 1428        |

#### MODBUS TCP client #3 statistics

| Parameter                                            | Description | Address(es) |
| ---------------------------------------------------- | ----------- | ----------- |
| Average receive speed, B/min                         |             | 1430        |
| Average transmit speed, B/min                        |             | 1431        |
| Number of received bytes since last connection, B    |             | 1432        |
| Number of transmitted bytes since last connection, B |             | 1434        |
| Number of received bytes since restart, B            |             | 1436        |
| Number of transmitted bytes since restart, B         |             | 1438        |

#### MODBUS TCP client #4 statistics

| Parameter                                            | Description | Address(es) |
| ---------------------------------------------------- | ----------- | ----------- |
| Average receive speed, B/min                         |             | 1440        |
| Average transmit speed, B/min                        |             | 1441        |
| Number of received bytes since last connection, B    |             | 1442        |
| Number of transmitted bytes since last connection, B |             | 1444        |
| Number of received bytes since restart, B            |             | 1446        |
| Number of transmitted bytes since restart, B         |             | 1448        |

#### Serial port #1 statistics

| Parameter                                    | Description | Address(es) |
| -------------------------------------------- | ----------- | ----------- |
| Average receive speed, B/min                 |             | 1450        |
| Average transmit speed, B/min                |             | 1451        |
| Number of received bytes since restart, B    |             | 1452        |
| Number of transmitted bytes since restart, B |             | 1454        |

#### Serial port #2 statistics

| Parameter                                    | Description | Address(es) |
| -------------------------------------------- | ----------- | ----------- |
| Average receive speed, B/min                 |             | 1456        |
| Average transmit speed, B/min                |             | 1457        |
| Number of received bytes since restart, B    |             | 1458        |
| Number of transmitted bytes since restart, B |             | 1460        |

#### Serial port #3 statistics

| Parameter                                    | Description | Address(es) |
| -------------------------------------------- | ----------- | ----------- |
| Average receive speed, B/min                 |             | 1462        |
| Average transmit speed, B/min                |             | 1463        |
| Number of received bytes since restart, B    |             | 1464        |
| Number of transmitted bytes since restart, B |             | 1466        |

#### Serial port #4 statistics

| Parameter                                    | Description | Address(es) |
| -------------------------------------------- | ----------- | ----------- |
| Average receive speed, B/min                 |             | 1468        |
| Average transmit speed, B/min                |             | 1469        |
| Number of received bytes since restart, B    |             | 1470        |
| Number of transmitted bytes since restart, B |             | 1472        |

#### MODBUS TCP server statistics

| Parameter                                    | Description | Address(es) |
| -------------------------------------------- | ----------- | ----------- |
| Average receive speed, B/min                 |             | 1474        |
| Average transmit speed, B/min                |             | 1475        |
| Number of received bytes since restart, B    |             | 1476        |
| Number of transmitted bytes since restart, B |             | 1478        |

#### WireGuard statistics

| Parameter                                       | Description | Address(es) |
| ----------------------------------------------- | ----------- | ----------- |
| Number of received bytes since OS restart, B    |             | 1480        |
| Number of transmitted bytes since OS restart, B |             | 1482        |

### Group of WireGuard state parameters

| Parameter                               | Description                                                                                    | Address(es) |
| --------------------------------------- | ---------------------------------------------------------------------------------------------- | ----------- |
| Current IP address of the WireGuard VPN |                                                                                                | 1484...1487 |
| Net prefix                              |                                                                                                | 1488        |
| WireGuard VPN status                    | 0 - unavailable;<br>1 - no address;<br>2 - no connection to server;<br>3 - connected to server | 1489        |
| Server IP address of the WireGuard VPN  |                                                                                                | 1490...1493 |
| Server ping, ms                         |                                                                                                | 1494        |

### Group of interface settings parameters

During the initial setup of the device, it is usually necessary to set these
parameters in order to communicate with the device and to connect it to the
Internet.

#### Ethernet Settings

| Parameter       | Permissible values | Factory default                  | Description | Address(es) |
| --------------- | ------------------ | -------------------------------- | ----------- | ----------- |
| IP address      | IPv4 address       | 192.168.1.120                    |             | 1500...1503 |
| Subnet mask     | IPv4 address       | 255.255.255.0                    |             | 1504...1507 |
| Gateway address | IPv4 address       | 192.168.1.1                      |             | 1508...1511 |
| DNS 1 address   | IPv4 address       | 192.168.1.1                      |             | 1512...1515 |
| DNS 2 address   | IPv4 address       | 8.8.8.8                          |             | 1516...1519 |
| DHCP usage      | 0 / 1              | 1                                |             | 1520        |
| Device name     | Character string   | Depends on OS (`eth0` as a rule) |             | 1530...1549 |

#### Wi-Fi settings

| Parameter                           | Permissible values | Factory default                   | Description                                                                                                                             | Address(es) |
| ----------------------------------- | ------------------ | --------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| IP address of the own access point  | IPv4 address       | `192.168.4.1`                     | Address is used in access point mode (entered in the browser to connect to product and configure it)                                    | 1700...1703 |
| Subnet mask of the own access point | IPv4 address       | `255.255.255.0`                   |                                                                                                                                         | 1704...1707 |
| Wi-Fi mode                          | 0 / 1 / 2          | 0                                 | 0 – Wi-Fi off;<br>1 – Wi-Fi station (client) ;<br>2 – Wi-Fi access point hotspot (server)                                               | 1708        |
| ISO1366 country code                | 1...999            | 616                               | To use Wi-Fi, the correct country code of the transmitter location should be specified                                                  | 1709        |
| Device name                         | Character string   | Depends on OS (`wlan0` as a rule) |                                                                                                                                         | 1730...1749 |
| SSID of the own access point        | Character string   | -                                 | Empty string means the default name `OPCB_XXXXXX` (where XXXXXXXX is the last 6 characters of the MAC address on the unit label)        | 1750...1781 |
| Password of the own access point    | Character string   | `12345678`                        | The parameter is only available for writing. When read, the string `********` is returned (or an empty string if there is no password). | 1785...1848 |
| IP address                          | IPv4 address       | `192.168.1.121`                   |                                                                                                                                         | 1850...1853 |
| Subnet mask                         | IPv4 address       | `255.255.255.0`                   |                                                                                                                                         | 1854...1857 |
| Gateway address                     | IPv4 address       | `192.168.1.1`                     |                                                                                                                                         | 1858...1861 |
| DNS 1 address                       | IPv4 address       | `192.168.1.1`                     |                                                                                                                                         | 1862...1865 |
| DNS 2 address                       | IPv4 address       | `8.8.8.8`                         |                                                                                                                                         | 1866...1869 |
| DHCP usage                          | 0 / 1              | 1                                 |                                                                                                                                         | 1870        |
| SSID of the access point            | Character string   | `YOUR_SSID`                       |                                                                                                                                         | 1900...1931 |
| Access point password               | Character string   | `YOUR_PASSWORD`                   | The parameter is only available for writing. When read, the string `********` is returned (or an empty string if there is no password). | 1935...1998 |

### Group of user registers

| Parameter | Permissible values | Initial value | Description                                                                                            | Address(es) |
| --------- | ------------------ | ------------- | ------------------------------------------------------------------------------------------------------ | ----------- |
| User      | 0...65535          | 0             | Can be used to transfer data between the cloud server and other devices. The data is reset on restart. | 5000...5999 |
