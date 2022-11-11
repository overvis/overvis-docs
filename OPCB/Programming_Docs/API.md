# OPCB-221 HTTP API

Avalable endpoints:

- `/api/ver`
- `/api/static.json`
- `/api/get_sta.json`
- `/api/list_ser.json`, `/api/list_nif.json`, `/api/list_ap.json`
- `/api/set_conf.json`, `/api/get_conf.son`
- `/api/cmd.json`
- `/api/mbgate.json`

## `/api/ver`: Get device version

Returns information about the version of the device.

No query parameters or additional request data.

Response is a JSON object with the following fields:

| Parameter    | Value Range | Description                                         |
| ------------ | ----------- | --------------------------------------------------- |
| `dev_type`   | 42          | Manufacturer identification code (`42` - OPCB-221). |
| `ver`        | String      | Version of the communication software.              |
| `ver_commit` | String      | Version code and date.                              |

_Example:_

Read the version information:

`/api/ver`

```json
{
  "dev_type": 42,
  "ver": "OPCB-221-rpi3/0.0.3",
  "ver_commit": "03ce15bf (2022-06-01 15:30:01.445)"
}
```

## `/api/static.json`: Get device version

Returns information about the device, which is gathered at the start of the communication software.

No query parameters or additional request data.

Response is a JSON object with the following fields:

| Parameter              | Value Range | Description                                                                                |
| ---------------------- | ----------- | ------------------------------------------------------------------------------------------ |
| `dev_key`              | String      | Private key used to establish WireGuard protected VPN link to the cloud server.            |
| `dev_key_pub`          | String      | Public key used to establish WireGuard protected VPN link to the cloud server.             |
| `dev_mac`              | MAC-48      | Globally unique MAC address used for identification by the cloud server.                   |
| `dev_model_name`       | String      | Device model name.                                                                         |
| `dev_pin_code`         | String      | Device 8-character PIN code used to link it to the cloud server account.                   |
| `dev_type`             | 42          | Manufacturer identification code (`42` - OPCB-221 series).                                 |
| `mf_batch_description` | String      | Description of the device registration license read from the `/MANUFACTURER_LICENSE` file. |
| `mf_license_password`  | String      | Password of the device registration license read from the `/MANUFACTURER_LICENSE` file.    |
| `mf_license_uuid`      | String      | ID number of the device registration license read from the `/MANUFACTURER_LICENSE` file.   |
| `mf_open`              | 0 / 1       | Is device registration license file `/MANUFACTURER_LICENSE` present.                       |
| `ver`                  | String      | Version of the communication software.                                                     |
| `ver_commit`           | String      | Version code and date.                                                                     |

_Example:_

Read the static information:

`/api/static.json`

```json
{
  "dev_key": "ABCDEFGHABCDEFGHABCDEFGHABCDEFGHABCDEFGHABC=",
  "dev_key_pub": "ABCABCDEFGHABCDEFGHABCDEFGHABCDEFGHABCDEFGH=",
  "dev_mac": "02:81:01:02:03:04",
  "dev_model_name": "OPCB-221",
  "dev_pin_code": "00000000",
  "dev_type": 42,
  "mf_batch_description": "",
  "mf_license_password": "",
  "mf_license_uuid": "",
  "mf_open": "0",
  "ver": "OPCB-221-rpi3/0.0.3",
  "ver_commit": "03ce15bf (2022-06-01 15:30:01.445)"
}
```

## `/api/get_sta.json`: Get status parameters values

Returns current values of all status parameters.

Response is a JSON object with the following fields:

| Parameter                     | Value Range                                 | Description                                                                                                                                                                                                                                                                                                                                                     |
| ----------------------------- | ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `core_temp_cdegc`             | -29999 / -27315 ... 27315 / 29999           | Core temperature in centidegrees (Celsius);<br>-29999 - unknown.                                                                                                                                                                                                                                                                                                |
| `core_volt_mv`                | 0 / 500 ... 6000 / 9999                     | Core supply voltage in millivolts;<br>0 - unknown.                                                                                                                                                                                                                                                                                                              |
| `elan_enabled`                | 0 / 1                                       | Is Ethernet enabled in configuration.                                                                                                                                                                                                                                                                                                                           |
| `elan_gateway`                | IPv4                                        | Ethernet interface gateway IP address.                                                                                                                                                                                                                                                                                                                          |
| `elan_gateway_ping_ms`        | 0 ... 999 / 1000                            | Ethernet gateway reponse time in milliseconds;<br>1000 - response timeout.                                                                                                                                                                                                                                                                                      |
| `elan_ip`                     | IPv4                                        | Ethernet interface IP address.                                                                                                                                                                                                                                                                                                                                  |
| `elan_net_prefix`             | 0 ... 32                                    | Ethernet net prefix (mask length).                                                                                                                                                                                                                                                                                                                              |
| `elan_state`                  | 0 / 1 / 2 / 3                               | Ethernet interface state:<br>0 - unavailable;<br>1 – no IP address;<br>2 - gateway not connected (offline);<br>3 - gateway connected (online).                                                                                                                                                                                                                  |
| `inet_1_ip`                   | IPv4                                        | Internet check node #1                                                                                                                                                                                                                                                                                                                                          |
| `inet_1_ping_ms`              | 0 ... 999 / 1000                            | Internet node #1 reponse time in milliseconds;<br>1000 - response timeout.                                                                                                                                                                                                                                                                                      |
| `inet_2_ip`                   | IPv4                                        | Internet check node #2                                                                                                                                                                                                                                                                                                                                          |
| `inet_2_ping_ms`              | 0 ... 999 / 1000                            | Internet node #2 reponse time in milliseconds;<br>1000 - response timeout.                                                                                                                                                                                                                                                                                      |
| `inet_state`                  | 2 / 3                                       | Internet state:<br>2 - offline;<br>3 - online.                                                                                                                                                                                                                                                                                                                  |
| `mbtcp_cli_0_conn_attempt`    | 0 ... 999                                   | Number of unsuccessful attempts of MODBUS TCP client #1 connection to the remote server.                                                                                                                                                                                                                                                                        |
| `mbtcp_cli_0_conn_timestamp`  | Time                                        | Time of the last connection of MODBUS TCP client #1 (seconds since the start of the year 1970).                                                                                                                                                                                                                                                                 |
| `mbtcp_cli_0_enabled`         | 0 / 1                                       | Is MODBUS TCP client #1 enabled in configuration.                                                                                                                                                                                                                                                                                                               |
| `mbtcp_cli_0_last_error_code` | -999 999 999 ... -1 / 0                     | Last MODBUS TCP client #1 connection attempt error code;<br>0 - no error.                                                                                                                                                                                                                                                                                       |
| `mbtcp_cli_0_netif`           | 0 / 2 / 3 / 255                             | MODBUS TCP client #1 connection:<br>0 - no connection;<br>2 – Ethernet;<br>3 - Wi-Fi;<br>255 - other interface.                                                                                                                                                                                                                                                 |
| `mbtcp_cli_0_route`           | MODBUS route                                | Requests to addresses in any range of this route are gated to MODBUS TCP client #1 (string of comma-separated MODBUS address ranges in form of `address-address`).                                                                                                                                                                                              |
| `mbtcp_cli_0_rx_avg`          | 0 ... 60 000                                | Average of bytes per minute speed of reception by the MODBUS TCP client #1.                                                                                                                                                                                                                                                                                     |
| `mbtcp_cli_0_rx_conn`         | 0 ... 1 000 000 000                         | Number of bytes received by the MODBUS TCP client #1 since the last connection.                                                                                                                                                                                                                                                                                 |
| `mbtcp_cli_0_rx_total`        | 0 ... 1 000 000 000                         | Number of bytes received by the MODBUS TCP client #1 since the last restart.                                                                                                                                                                                                                                                                                    |
| `mbtcp_cli_0_tx_avg`          | 0 ... 60 000                                | Average of bytes per minute speed of transmission by the MODBUS TCP client #1.                                                                                                                                                                                                                                                                                  |
| `mbtcp_cli_0_tx_conn`         | 0 ... 1 000 000 000                         | Number of bytes transmitted by the MODBUS TCP client #1 since the last connection.                                                                                                                                                                                                                                                                              |
| `mbtcp_cli_0_tx_total`        | 0 ... 1 000 000 000                         | Number of bytes transmitted by the MODBUS TCP client #1 since the last restart.                                                                                                                                                                                                                                                                                 |
| `mbtcp_cli_1_conn_attempt`    | 0 ... 999                                   | Number of unsuccessful attempts of MODBUS TCP client #2 connection to the remote server.                                                                                                                                                                                                                                                                        |
| `mbtcp_cli_1_conn_timestamp`  | Time                                        | Time of the last connection of MODBUS TCP client #2 (seconds since the start of the year 1970).                                                                                                                                                                                                                                                                 |
| `mbtcp_cli_1_enabled`         | 0 / 1                                       | Is MODBUS TCP client #2 enabled in configuration.                                                                                                                                                                                                                                                                                                               |
| `mbtcp_cli_1_last_error_code` | -999 999 999 ... -1 / 0                     | Last MODBUS TCP client #2 connection attempt error code;<br>0 - no error.                                                                                                                                                                                                                                                                                       |
| `mbtcp_cli_1_netif`           | 0 / 2 / 3 / 255                             | MODBUS TCP client #2 connection:<br>0 - no connection;<br>2 – Ethernet;<br>3 - Wi-Fi;<br>255 - other interface.                                                                                                                                                                                                                                                 |
| `mbtcp_cli_1_route`           | MODBUS route                                | Requests to addresses in any range of this route are gated to MODBUS TCP client #2 (string of comma-separated MODBUS address ranges in form of `address-address`).                                                                                                                                                                                              |
| `mbtcp_cli_1_rx_avg`          | 0 ... 60 000                                | Average of bytes per minute speed of reception by the MODBUS TCP client #2.                                                                                                                                                                                                                                                                                     |
| `mbtcp_cli_1_rx_conn`         | 0 ... 1 000 000 000                         | Number of bytes received by the MODBUS TCP client #2 since the last connection.                                                                                                                                                                                                                                                                                 |
| `mbtcp_cli_1_rx_total`        | 0 ... 1 000 000 000                         | Number of bytes received by the MODBUS TCP client #2 since the last restart.                                                                                                                                                                                                                                                                                    |
| `mbtcp_cli_1_tx_avg`          | 0 ... 60 000                                | Average of bytes per minute speed of transmission by the MODBUS TCP client #2.                                                                                                                                                                                                                                                                                  |
| `mbtcp_cli_1_tx_conn`         | 0 ... 1 000 000 000                         | Number of bytes transmitted by the MODBUS TCP client #2 since the last connection.                                                                                                                                                                                                                                                                              |
| `mbtcp_cli_1_tx_total`        | 0 ... 1 000 000 000                         | Number of bytes transmitted by the MODBUS TCP client #2 since the last restart.                                                                                                                                                                                                                                                                                 |
| `mbtcp_cli_2_conn_attempt`    | 0 ... 999                                   | Number of unsuccessful attempts of MODBUS TCP client #3 connection to the remote server.                                                                                                                                                                                                                                                                        |
| `mbtcp_cli_2_conn_timestamp`  | Time                                        | Time of the last connection of MODBUS TCP client #3 (seconds since the start of the year 1970).                                                                                                                                                                                                                                                                 |
| `mbtcp_cli_2_enabled`         | 0 / 1                                       | Is MODBUS TCP client #3 enabled in configuration.                                                                                                                                                                                                                                                                                                               |
| `mbtcp_cli_2_last_error_code` | -999 999 999 ... -1 / 0                     | Last MODBUS TCP client #3 connection attempt error code;<br>0 - no error.                                                                                                                                                                                                                                                                                       |
| `mbtcp_cli_2_netif`           | 0 / 2 / 3 / 255                             | MODBUS TCP client #3 connection:<br>0 - no connection;<br>2 – Ethernet;<br>3 - Wi-Fi;<br>255 - other interface.                                                                                                                                                                                                                                                 |
| `mbtcp_cli_2_route`           | MODBUS route                                | Requests to addresses in any range of this route are gated to MODBUS TCP client #3 (string of comma-separated MODBUS address ranges in form of `address-address`).                                                                                                                                                                                              |
| `mbtcp_cli_2_rx_avg`          | 0 ... 60 000                                | Average of bytes per minute speed of reception by the MODBUS TCP client #3.                                                                                                                                                                                                                                                                                     |
| `mbtcp_cli_2_rx_conn`         | 0 ... 1 000 000 000                         | Number of bytes received by the MODBUS TCP client #3 since the last connection.                                                                                                                                                                                                                                                                                 |
| `mbtcp_cli_2_rx_total`        | 0 ... 1 000 000 000                         | Number of bytes received by the MODBUS TCP client #3 since the last restart.                                                                                                                                                                                                                                                                                    |
| `mbtcp_cli_2_tx_avg`          | 0 ... 60 000                                | Average of bytes per minute speed of transmission by the MODBUS TCP client #3.                                                                                                                                                                                                                                                                                  |
| `mbtcp_cli_2_tx_conn`         | 0 ... 1 000 000 000                         | Number of bytes transmitted by the MODBUS TCP client #3 since the last connection.                                                                                                                                                                                                                                                                              |
| `mbtcp_cli_2_tx_total`        | 0 ... 1 000 000 000                         | Number of bytes transmitted by the MODBUS TCP client #3 since the last restart.                                                                                                                                                                                                                                                                                 |
| `mbtcp_cli_3_conn_attempt`    | 0 ... 999                                   | Number of unsuccessful attempts of MODBUS TCP client #4 connection to the remote server.                                                                                                                                                                                                                                                                        |
| `mbtcp_cli_3_conn_timestamp`  | Time                                        | Time of the last connection of MODBUS TCP client #4 (seconds since the start of the year 1970).                                                                                                                                                                                                                                                                 |
| `mbtcp_cli_3_enabled`         | 0 / 1                                       | Is MODBUS TCP client #4 enabled in configuration.                                                                                                                                                                                                                                                                                                               |
| `mbtcp_cli_3_last_error_code` | -999 999 999 ... -1 / 0                     | Last MODBUS TCP client #4 connection attempt error code;<br>0 - no error.                                                                                                                                                                                                                                                                                       |
| `mbtcp_cli_3_netif`           | 0 / 2 / 3 / 255                             | MODBUS TCP client #4 connection:<br>0 - no connection;<br>2 – Ethernet;<br>3 - Wi-Fi;<br>255 - other interface.                                                                                                                                                                                                                                                 |
| `mbtcp_cli_3_route`           | MODBUS route                                | Requests to addresses in any range of this route are gated to MODBUS TCP client #4 (string of comma-separated MODBUS address ranges in form of `address-address`).                                                                                                                                                                                              |
| `mbtcp_cli_3_rx_avg`          | 0 ... 60 000                                | Average of bytes per minute speed of reception by the MODBUS TCP client #4.                                                                                                                                                                                                                                                                                     |
| `mbtcp_cli_3_rx_conn`         | 0 ... 1 000 000 000                         | Number of bytes received by the MODBUS TCP client #4 since the last connection.                                                                                                                                                                                                                                                                                 |
| `mbtcp_cli_3_rx_total`        | 0 ... 1 000 000 000                         | Number of bytes received by the MODBUS TCP client #4 since the last restart.                                                                                                                                                                                                                                                                                    |
| `mbtcp_cli_3_tx_avg`          | 0 ... 60 000                                | Average of bytes per minute speed of transmission by the MODBUS TCP client #4.                                                                                                                                                                                                                                                                                  |
| `mbtcp_cli_3_tx_conn`         | 0 ... 1 000 000 000                         | Number of bytes transmitted by the MODBUS TCP client #4 since the last connection.                                                                                                                                                                                                                                                                              |
| `mbtcp_cli_3_tx_total`        | 0 ... 1 000 000 000                         | Number of bytes transmitted by the MODBUS TCP client #4 since the last restart.                                                                                                                                                                                                                                                                                 |
| `mbtcp_srv_clients`           | 0 ... 20                                    | The number of clients connected to the MODBUS TCP server.                                                                                                                                                                                                                                                                                                       |
| `mbtcp_srv_clients_list`      | String                                      | List of clients connected to the MODBUS TCP server (string of comma-separated items in form of `IPv4:port (interface)`).                                                                                                                                                                                                                                        |
| `mbtcp_srv_enabled`           | 0 / 1                                       | Is MODBUS TCP server enabled in configuration                                                                                                                                                                                                                                                                                                                   |
| `mbtcp_srv_rx_avg`            | 0 ... 60 000                                | Average of bytes per minute speed of reception by the MODBUS TCP server (from all the clients).                                                                                                                                                                                                                                                                 |
| `mbtcp_srv_rx_total`          | 0 ... 1 000 000 000                         | Number of bytes received by the MODBUS TCP server (from all the clients) since the last restart.                                                                                                                                                                                                                                                                |
| `mbtcp_srv_tx_avg`            | 0 ... 60 000                                | Average of bytes per minute speed of transmission by the MODBUS TCP server (to all the clients).                                                                                                                                                                                                                                                                |
| `mbtcp_srv_tx_total`          | 0 ... 1 000 000 000                         | Number of bytes transmitted by the MODBUS TCP server (to all the clients) since the last restart.                                                                                                                                                                                                                                                               |
| `os_cur_time`                 | Time                                        | System time (seconds since the start of the year 1970).                                                                                                                                                                                                                                                                                                         |
| `os_run_time`                 | 0 ... 1 000 000 000                         | System operation time, number of seconds since the OS restart.                                                                                                                                                                                                                                                                                                  |
| `ovb_act_code`                | 0 / 10 000 000 ... 99 999 999 / 100 000 000 | An 8-digit code that is transmitted by the cloud server when connecting back and can be read locally by the user. When the user enters the code on the cloud server, this reverse connection is activated (transmitted to the given user on the server). 0 - connection activated; 10 000 000 ... 99 999 999 - activation code; 100 000 000 - no code assigned. |
| `ovb_conn_attempt`            | 0 ... 999                                   | Number of unsuccessful attempts of connecting to the Cloud server.                                                                                                                                                                                                                                                                                              |
| `ovb_conn_timestamp`          | Time                                        | Time of the last connection to the Cloud server (seconds since the start of the year 1970).                                                                                                                                                                                                                                                                     |
| `ovb_cur_hostname`            | String                                      | Cloud server hostname                                                                                                                                                                                                                                                                                                                                           |
| `ovb_last_error_code`         | -20 ... 0                                   | The code of the last connection error to the cloud server;<br>0 - no error.                                                                                                                                                                                                                                                                                     |
| `ovb_netif`                   | 0 / 2 / 3 / 255                             | Cloud server connection:<br>0 - no connection;<br>2 – Ethernet;<br>3 - Wi-Fi;<br>255 - other interface.                                                                                                                                                                                                                                                         |
| `ovb_rx_avg`                  | 0 ... 60 000                                | Average of bytes per minute speed of reception from the Cloud server.                                                                                                                                                                                                                                                                                           |
| `ovb_rx_conn`                 | 0 ... 1 000 000 000                         | Number of bytes received from the Cloud server since the last connection.                                                                                                                                                                                                                                                                                       |
| `ovb_rx_total`                | 0 ... 1 000 000 000                         | Number of bytes received from the Cloud server since the last restart.                                                                                                                                                                                                                                                                                          |
| `ovb_state`                   | 0 / 1 / 2 / 3                               | State of connection to the cloud server:<br>0 - prohibited in settings;<br>1 - delay between the attempts;<br>2 - connection being established;<br>3 - connected.                                                                                                                                                                                               |
| `ovb_tx_avg`                  | 0 ... 60 000                                | Average of bytes per minute speed of transmission to the Cloud server.                                                                                                                                                                                                                                                                                          |
| `ovb_tx_conn`                 | 0 ... 1 000 000 000                         | Number of bytes transmitted to the Cloud server since the last connection.                                                                                                                                                                                                                                                                                      |
| `ovb_tx_total`                | 0 ... 1 000 000 000                         | Number of bytes transmitted to the Cloud server since the last restart.                                                                                                                                                                                                                                                                                         |
| `ovm_bind_server`             | String                                      | Cloud server which the device is binded to in configuration                                                                                                                                                                                                                                                                                                     |
| `rs485_0_attempt`             | 0 ... 999                                   | Number of unsuccessful attempts of opening the serial interface #1 port.                                                                                                                                                                                                                                                                                        |
| `rs485_0_dev_name`            | String                                      | Serial interface #1 OS device name in configuration                                                                                                                                                                                                                                                                                                             |
| `rs485_0_error`               | -999 999 999 ... -1 / 0                     | Last serial interface #1 port open attempt error code;<br>0 - no error.                                                                                                                                                                                                                                                                                         |
| `rs485_0_mode`                | 0 / 1 / 2 / 3                               | Serial interface #1 mode:<br>0 - started RTU Master;<br>1 - started ASCII Master;<br>2 - started RTU Slave;<br>3 - started ASCII Slave.                                                                                                                                                                                                                         |
| `rs485_0_route`               | MODBUS route                                | Requests to addresses in any range of this route are gated to serial interface #1 (string of comma-separated MODBUS address ranges in form of `address-address`).                                                                                                                                                                                               |
| `rs485_0_rx_avg`              | 0 ... 60 000                                | Average of bytes per minute speed of reception by the serial interface #1.                                                                                                                                                                                                                                                                                      |
| `rs485_0_rx_total`            | 0 ... 1 000 000 000                         | Number of bytes received by the serial interface #1 since the last restart.                                                                                                                                                                                                                                                                                     |
| `rs485_0_status`              | 0 / 1 / 2                                   | State of the serial interface #1:<br>0 - disabled in settings;<br>1 - port being opened;<br>2 - port open.                                                                                                                                                                                                                                                      |
| `rs485_0_tx_avg`              | 0 ... 60 000                                | Average of bytes per minute speed of transmission by the serial interface #1.                                                                                                                                                                                                                                                                                   |
| `rs485_0_tx_total`            | 0 ... 1 000 000 000                         | Number of bytes transmitted by the serial interface #1 since the last restart.                                                                                                                                                                                                                                                                                  |
| `rs485_1_attempt`             | 0 ... 999                                   | Number of unsuccessful attempts of opening the serial interface #2 port.                                                                                                                                                                                                                                                                                        |
| `rs485_1_dev_name`            | String                                      | Serial interface #2 OS device name in configuration                                                                                                                                                                                                                                                                                                             |
| `rs485_1_error`               | -999 999 999 ... -1 / 0                     | Last serial interface #2 port open attempt error code;<br>0 - no error.                                                                                                                                                                                                                                                                                         |
| `rs485_1_mode`                | 0 / 1 / 2 / 3                               | Serial interface #2 mode:<br>0 - started RTU Master;<br>1 - started ASCII Master;<br>2 - started RTU Slave;<br>3 - started ASCII Slave.                                                                                                                                                                                                                         |
| `rs485_1_route`               | MODBUS route                                | Requests to addresses in any range of this route are gated to serial interface #2 (string of comma-separated MODBUS address ranges in form of `address-address`).                                                                                                                                                                                               |
| `rs485_1_rx_avg`              | 0 ... 60 000                                | Average of bytes per minute speed of reception by the serial interface #2.                                                                                                                                                                                                                                                                                      |
| `rs485_1_rx_total`            | 0 ... 1 000 000 000                         | Number of bytes received by the serial interface #2 since the last restart.                                                                                                                                                                                                                                                                                     |
| `rs485_1_status`              | 0 / 1 / 2                                   | State of the serial interface #2:<br>0 - disabled in settings;<br>1 - port being opened;<br>2 - port open.                                                                                                                                                                                                                                                      |
| `rs485_1_tx_avg`              | 0 ... 60 000                                | Average of bytes per minute speed of transmission by the serial interface #2.                                                                                                                                                                                                                                                                                   |
| `rs485_1_tx_total`            | 0 ... 1 000 000 000                         | Number of bytes transmitted by the serial interface #2 since the last restart.                                                                                                                                                                                                                                                                                  |
| `rs485_2_attempt`             | 0 ... 999                                   | Number of unsuccessful attempts of opening the serial interface #3 port.                                                                                                                                                                                                                                                                                        |
| `rs485_2_dev_name`            | String                                      | Serial interface #3 OS device name in configuration                                                                                                                                                                                                                                                                                                             |
| `rs485_2_error`               | -999 999 999 ... -1 / 0                     | Last serial interface #3 port open attempt error code;<br>0 - no error.                                                                                                                                                                                                                                                                                         |
| `rs485_2_mode`                | 0 / 1 / 2 / 3                               | Serial interface #3 mode:<br>0 - started RTU Master;<br>1 - started ASCII Master;<br>2 - started RTU Slave;<br>3 - started ASCII Slave.                                                                                                                                                                                                                         |
| `rs485_2_route`               | MODBUS route                                | Requests to addresses in any range of this route are gated to serial interface #3 (string of comma-separated MODBUS address ranges in form of `address-address`).                                                                                                                                                                                               |
| `rs485_2_rx_avg`              | 0 ... 60 000                                | Average of bytes per minute speed of reception by the serial interface #3.                                                                                                                                                                                                                                                                                      |
| `rs485_2_rx_total`            | 0 ... 1 000 000 000                         | Number of bytes received by the serial interface #3 since the last restart.                                                                                                                                                                                                                                                                                     |
| `rs485_2_status`              | 0 / 1 / 2                                   | State of the serial interface #3:<br>0 - disabled in settings;<br>1 - port being opened;<br>2 - port open.                                                                                                                                                                                                                                                      |
| `rs485_2_tx_avg`              | 0 ... 60 000                                | Average of bytes per minute speed of transmission by the serial interface #3.                                                                                                                                                                                                                                                                                   |
| `rs485_2_tx_total`            | 0 ... 1 000 000 000                         | Number of bytes transmitted by the serial interface #3 since the last restart.                                                                                                                                                                                                                                                                                  |
| `rs485_3_attempt`             | 0 ... 999                                   | Number of unsuccessful attempts of opening the serial interface #4 port.                                                                                                                                                                                                                                                                                        |
| `rs485_3_dev_name`            | String                                      | Serial interface #4 OS device name in configuration                                                                                                                                                                                                                                                                                                             |
| `rs485_3_error`               | -999 999 999 ... -1 / 0                     | Last serial interface #4 port open attempt error code;<br>0 - no error.                                                                                                                                                                                                                                                                                         |
| `rs485_3_mode`                | 0 / 1 / 2 / 3                               | Serial interface #4 mode:<br>0 - started RTU Master;<br>1 - started ASCII Master;<br>2 - started RTU Slave;<br>3 - started ASCII Slave.                                                                                                                                                                                                                         |
| `rs485_3_route`               | MODBUS route                                | Requests to addresses in any range of this route are gated to serial interface #4 (string of comma-separated MODBUS address ranges in form of `address-address`).                                                                                                                                                                                               |
| `rs485_3_rx_avg`              | 0 ... 60 000                                | Average of bytes per minute speed of reception by the serial interface #4.                                                                                                                                                                                                                                                                                      |
| `rs485_3_rx_total`            | 0 ... 1 000 000 000                         | Number of bytes received by the serial interface #4 since the last restart.                                                                                                                                                                                                                                                                                     |
| `rs485_3_status`              | 0 / 1 / 2                                   | State of the serial interface #4:<br>0 - disabled in settings;<br>1 - port being opened;<br>2 - port open.                                                                                                                                                                                                                                                      |
| `rs485_3_tx_avg`              | 0 ... 60 000                                | Average of bytes per minute speed of transmission by the serial interface #4.                                                                                                                                                                                                                                                                                   |
| `rs485_3_tx_total`            | 0 ... 1 000 000 000                         | Number of bytes transmitted by the serial interface #4 since the last restart.                                                                                                                                                                                                                                                                                  |
| `runtime_start_timestamp`     | Time                                        | Time of the communication software start (seconds since the start of the year 1970).                                                                                                                                                                                                                                                                            |
| `sys_mem_total_kb`            | 0 ... 1 000 000 000                         | Volume of available memory in kB.                                                                                                                                                                                                                                                                                                                               |
| `sys_mem_used_kb`             | 0 ... 1 000 000 000                         | Exhausted memory size in kB.                                                                                                                                                                                                                                                                                                                                    |
| `sys_vol_total_kb`            | 0 ... 1 000 000 000                         | System partition size in kB.                                                                                                                                                                                                                                                                                                                                    |
| `sys_vol_used_kb`             | 0 ... 1 000 000 000                         | Capacity of the system partition in kB.                                                                                                                                                                                                                                                                                                                         |
| `viu_enabled`                 | 0 / 1                                       | Is internal virtual device enabled in configuration.                                                                                                                                                                                                                                                                                                            |
| `viu_route`                   | MODBUS route                                | Requests to addresses in any range of this route are gated to internal virtual device (string of comma-separated MODBUS address ranges in form of `address-address`).                                                                                                                                                                                           |
| `wg_ip`                       | IPv4                                        | WireGuard VPN interface IP address.                                                                                                                                                                                                                                                                                                                             |
| `wg_net_prefix`               | 0 ... 32                                    | WireGuard VPN net prefix (mask length).                                                                                                                                                                                                                                                                                                                         |
| `wg_public_key`               | String                                      | WireGuard VPN public key                                                                                                                                                                                                                                                                                                                                        |
| `wg_rx_total`                 | 0 ... 1 000 000 000                         | Number of bytes received by the WireGuard VPN since the OS restart                                                                                                                                                                                                                                                                                              |
| `wg_server_hostname`          | String                                      | WireGuard control server host name.                                                                                                                                                                                                                                                                                                                             |
| `wg_server_ip`                | IPv4                                        | WireGuard VPN interface server IP address.                                                                                                                                                                                                                                                                                                                      |
| `wg_server_ping_ms`           | 0...999 / 1000                              | WireGuard VPN server reponse time in milliseconds;<br>1000 - response timeout.                                                                                                                                                                                                                                                                                  |
| `wg_state`                    | 0 / 1 / 2 / 3                               | WireGuard VPN interface state:<br>0 - unavailable;<br>1 – no IP address;<br>2 - server not connected (offline);<br>3 - server connected (online).                                                                                                                                                                                                               |
| `wg_tx_total`                 | 0 ... 1 000 000 000                         | Number of bytes transmitted by the WireGuard VPN since the OS restart                                                                                                                                                                                                                                                                                           |
| `wlan_enabled`                | 0 / 1                                       | Is Wi-Fi enabled in configuration.                                                                                                                                                                                                                                                                                                                              |
| `wlan_gateway`                | IPv4                                        | Wi-Fi interface gateway IP address.                                                                                                                                                                                                                                                                                                                             |
| `wlan_gateway_ping_ms`        | 0 ... 999 / 1000                            | Wi-Fi gateway reponse time in milliseconds;<br>1000 - response timeout.                                                                                                                                                                                                                                                                                         |
| `wlan_ip`                     | IPv4                                        | Wi-Fi interface IP address.                                                                                                                                                                                                                                                                                                                                     |
| `wlan_mode`                   | 0 / 1 / 2                                   | Wi-Fi mode in configuration.                                                                                                                                                                                                                                                                                                                                    |
| `wlan_net_prefix`             | 0 ... 32                                    | Wi-Fi net prefix (mask length).                                                                                                                                                                                                                                                                                                                                 |
| `wlan_state`                  | 0 / 1 / 2 / 3                               | Wi-Fi interface state:<br>0 - unavailable;<br>1 – no IP address;<br>2 - gateway not connected (offline);<br>3 - gateway connected (online).                                                                                                                                                                                                                     |

_Example:_

Read the device status information:

`/api/get_sta.json`

```json
{
  "core_temp_cdegc": "4649",
  "core_volt_mv": "1251",
  "elan_enabled": "1",
  "elan_gateway": "192.168.0.1",
  "elan_gateway_ping_ms": "1",
  "elan_ip": "192.168.0.146",
  "elan_net_prefix": "24",
  "elan_state": "3",
  "inet_1_ip": "1.1.1.1",
  "inet_1_ping_ms": "38",
  "inet_2_ip": "8.8.8.8",
  "inet_2_ping_ms": "23",
  "inet_state": "3",
  "mbtcp_cli_0_conn_attempt": "0",
  "mbtcp_cli_0_conn_timestamp": "1659356762",
  "mbtcp_cli_0_enabled": "1",
  "mbtcp_cli_0_last_error_code": "0",
  "mbtcp_cli_0_netif": "2",
  "mbtcp_cli_0_route": "112-247",
  "mbtcp_cli_0_rx_avg": "0",
  "mbtcp_cli_0_rx_conn": "0",
  "mbtcp_cli_0_rx_total": "0",
  "mbtcp_cli_0_tx_avg": "0",
  "mbtcp_cli_0_tx_conn": "0",
  "mbtcp_cli_0_tx_total": "0",
  "mbtcp_cli_1_conn_attempt": "0",
  "mbtcp_cli_1_conn_timestamp": "0",
  "mbtcp_cli_1_enabled": "0",
  "mbtcp_cli_1_last_error_code": "-206",
  "mbtcp_cli_1_netif": "0",
  "mbtcp_cli_1_route": "0-0",
  "mbtcp_cli_1_rx_avg": "0",
  "mbtcp_cli_1_rx_conn": "0",
  "mbtcp_cli_1_rx_total": "0",
  "mbtcp_cli_1_tx_avg": "0",
  "mbtcp_cli_1_tx_conn": "0",
  "mbtcp_cli_1_tx_total": "0",
  "mbtcp_cli_2_conn_attempt": "0",
  "mbtcp_cli_2_conn_timestamp": "0",
  "mbtcp_cli_2_enabled": "0",
  "mbtcp_cli_2_last_error_code": "-206",
  "mbtcp_cli_2_netif": "0",
  "mbtcp_cli_2_route": "0-0",
  "mbtcp_cli_2_rx_avg": "0",
  "mbtcp_cli_2_rx_conn": "0",
  "mbtcp_cli_2_rx_total": "0",
  "mbtcp_cli_2_tx_avg": "0",
  "mbtcp_cli_2_tx_conn": "0",
  "mbtcp_cli_2_tx_total": "0",
  "mbtcp_cli_3_conn_attempt": "0",
  "mbtcp_cli_3_conn_timestamp": "0",
  "mbtcp_cli_3_enabled": "0",
  "mbtcp_cli_3_last_error_code": "-206",
  "mbtcp_cli_3_netif": "0",
  "mbtcp_cli_3_route": "0-0",
  "mbtcp_cli_3_rx_avg": "0",
  "mbtcp_cli_3_rx_conn": "0",
  "mbtcp_cli_3_rx_total": "0",
  "mbtcp_cli_3_tx_avg": "0",
  "mbtcp_cli_3_tx_conn": "0",
  "mbtcp_cli_3_tx_total": "0",
  "mbtcp_srv_clients": "2",
  "mbtcp_srv_clients_list": "192.168.0.101:39553 (eth0),192.168.0.103:47393 (eth0)",
  "mbtcp_srv_enabled": "1",
  "mbtcp_srv_rx_avg": "12",
  "mbtcp_srv_rx_total": "240",
  "mbtcp_srv_tx_avg": "11",
  "mbtcp_srv_tx_total": "220",
  "os_cur_time": "1659356764",
  "os_run_time": "2495",
  "ovb_act_code": "83251152",
  "ovb_conn_attempt": "0",
  "ovb_conn_timestamp": "1659356759",
  "ovb_cur_hostname": "modbus.overvis.com",
  "ovb_last_error_code": "0",
  "ovb_netif": "2",
  "ovb_rx_avg": "24",
  "ovb_rx_conn": "137",
  "ovb_rx_total": "137",
  "ovb_state": "3",
  "ovb_tx_avg": "22",
  "ovb_tx_conn": "153",
  "ovb_tx_total": "162",
  "ovm_bind_server": "",
  "rs485_0_attempt": "0",
  "rs485_0_dev_name": "ttyUSB0",
  "rs485_0_error": "0",
  "rs485_0_mode": "0",
  "rs485_0_route": "1-110,111-247",
  "rs485_0_rx_avg": "0",
  "rs485_0_rx_total": "0",
  "rs485_0_status": "2",
  "rs485_0_tx_avg": "0",
  "rs485_0_tx_total": "0",
  "rs485_1_attempt": "0",
  "rs485_1_dev_name": "",
  "rs485_1_error": "0",
  "rs485_1_mode": "0",
  "rs485_1_route": "0-0",
  "rs485_1_rx_avg": "0",
  "rs485_1_rx_total": "0",
  "rs485_1_status": "0",
  "rs485_1_tx_avg": "0",
  "rs485_1_tx_total": "0",
  "rs485_2_attempt": "0",
  "rs485_2_dev_name": "",
  "rs485_2_error": "0",
  "rs485_2_mode": "0",
  "rs485_2_route": "0-0",
  "rs485_2_rx_avg": "0",
  "rs485_2_rx_total": "0",
  "rs485_2_status": "0",
  "rs485_2_tx_avg": "0",
  "rs485_2_tx_total": "0",
  "rs485_3_attempt": "0",
  "rs485_3_dev_name": "",
  "rs485_3_error": "0",
  "rs485_3_mode": "0",
  "rs485_3_route": "0-0",
  "rs485_3_rx_avg": "0",
  "rs485_3_rx_total": "0",
  "rs485_3_status": "0",
  "rs485_3_tx_avg": "0",
  "rs485_3_tx_total": "0",
  "runtime_start_timestamp": "1659355833",
  "sys_mem_total_kb": "1022984",
  "sys_mem_used_kb": "769016",
  "sys_vol_total_kb": "29837336",
  "sys_vol_used_kb": "5233232",
  "viu_enabled": "1",
  "viu_route": "111-111",
  "wg_ip": "10.42.0.255",
  "wg_net_prefix": "24",
  "wg_public_key": "81A938B572A94F85E7275F785C693E8578D6D823",
  "wg_rx_total": "509",
  "wg_server_hostname": "motherland.overvis.com",
  "wg_server_ip": "10.42.0.1",
  "wg_server_ping_ms": "87",
  "wg_state": "3",
  "wg_tx_total": "509",
  "wlan_enabled": "1",
  "wlan_gateway": "192.168.0.1",
  "wlan_gateway_ping_ms": "7",
  "wlan_ip": "192.168.0.147",
  "wlan_mode": "1",
  "wlan_net_prefix": "24",
  "wlan_state": "3"
}
```

## `/api/list_ser.json`, `/api/list_nif.json`, `/api/list_ap.json`: Get interfaces information.

- `/api/list_ser.json` - get all serial ports information.
- `/api/list_nif.json` - get network interfaces information.
- `/api/list_ap.json` - get Wi-Fi access-points information.

Input:

`list_size` can be specified with a value from 1 to 20 to limit the list size.

The request parameter can be passed in the url string (if the request method is `GET`) or in the
HTTP payload of the request in the format `x-www-form-urlencoded` (if the request method `POST`).

Response is a JSON object with the following fields:

| Parameter     | Value Range      | Description                                                                                                                                                                                                                                                                                                                                                                                             |
| ------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `list_ser`    | Array of objects | Optional field is present if the query `/api/list_ser.json` succeeds. Contains an array of objects with serial port information with the fields:`dev_name` (device name).                                                                                                                                                                                                                               |
| `list_nif`    | Array of objects | Optional field is present if the query `/api/list_nif.json` succeeds. Contains an array of objects with information about network interfaces with the fields: `dev_name` (device name), `mac` (optional field with MAC address if non-zero address is known), `type` (optional type field if interface type was recognized, may contain `loopback` for local, `elan` for Ethernet or `wlan` for Wi-Fi). |
| `list_ap`     | Array of objects | Optional field is present if the query `/api/list_ap.json` succeeds. Contains an array of objects with information about Wi-Fi access points with the fields: `ssid` (access point name), `mac` (access point MAC address), `ignal` (radio signal level, dBm).                                                                                                                                          |
| `error_code`  | -32768...-1      | Optional field. May contain an error code.                                                                                                                                                                                                                                                                                                                                                              |
| `error_descr` | String           | Optional field. May contain a text description of the error (in English).                                                                                                                                                                                                                                                                                                                               |

_Example:_

Read the information about the serial ports:

`/api/list_ser.json`

```json
{ "list_ser": [{ "dev_name": "ttyUSB0" }] }
```

## `/api/get_conf.json`, `/api/set_conf.json`: Managing configuration parameters.

- `/api/set_conf.json` - set configuration parameters values.
- `/api/get_conf.json` - list configuration parameter values.

Configuration parameter names for all requests are the same as listed in `opcb-default.conf` file.
Incorrect names are ignored.

Read request (`/api/get_conf.json`) accept optional list of up to 120 names of configuration
parameters.

Write request (`/api/set_conf.json`) requires a list of pairs: parameter name, parameter value of
correct type (number or string). Write request is atomic, changes configuration are applied only if
the request is successful.

The request parameter can be passed in the url string (if the request method is `GET`) or in the
HTTP payload of the request in the format `x-www-form-urlencoded` (if the request method `POST`).

Response is a JSON object with the following fields:

| Parameter     | Value Range                  | Description                                                                                         |
| ------------- | ---------------------------- | --------------------------------------------------------------------------------------------------- |
| `params`      | Object, keys are param names | Optional field, present when the query is successfully executed. Contains current parameter values. |
| `error_code`  | -32768...-1                  | Optional field. May contain an error code.                                                          |
| `error_descr` | String                       | Optional field. May contain a text description of the error (in English).                           |
| `param`       | String                       | Optional field. May contain the name of the parameter, operation with which caused the error.       |

_Examples:_

Read MODBUS address of the device configuration parameter (`virtual_uid`):

`/api/get_conf.json?virtual_uid`

```json
{ "params": { "virtual_uid": 111 } }
```

Change RS-485 serial port 1 byte format to odd (code 4):

`/api/set_conf.json?rs485_0_parity_stop=4`

```json
{ "params": { "rs485_0_parity_stop": 4 } }
```

Change the mode of Wi-Fi interface to incorrect value (9):

`/api/set_conf.json?wlan_mode=9`

```json
{ "error_code": -6, "error_descr": "Value illegal", "param": "wlan_mode" }
```

## `/api/cmd.json`: Command execution

Executes a command:

| Command              | Description                                                                                                                                                                                                         |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `cmd_ovb_disconnect` | Disconnect from the cloud server (if a connection has been established). If the connection to the cloud server is allowed in the settings, the connection will be established again after the specified time.       |
| `cmd_ovb_deactivate` | Request a deactivation on the cloud server (for example, by doing this, OPCB-221 can be detached from the Overvis account and will receive a new activation code, which can be used to link it to another account). |
| `cmd_conf_def`       | Re-read the settings from the `opcb-default.conf` file to a copy in memory. This will discard any changes that have been made and not saved.                                                                        |
| `cmd_abort`          | Abort the runtime (in service mode it will start up again and all configuration changes will take effect).                                                                                                          |
| `cmd_abort_all`      | Restart the device (may be necessary for the correct application of some interface settings).                                                                                                                       |

Request should contain exactly one command name.

The request parameter can be passed in the url string (if the request method is `GET`) or in the
HTTP payload of the request in the format `x-www-form-urlencoded` (if the request method `POST`).

Response is a JSON object with the following fields:

| Parameter     | Value Range | Description                                    |
| ------------- | ----------- | ---------------------------------------------- |
| `error_code`  | -32768...0  | Contains an error code. 0 - success.           |
| `error_descr` | String      | Optional. Description of the error in english. |

_Examples:_

Save changes to configuration file `opcb-user.conf` (command `cmd_conf_c2f`):

`/api/cmd.json?cmd_conf_c2f`

```json
{ "error_code": 0 }
```

Close the runtime (restarts automatically with full configuration reload):

`/api/cmd.json?cmd_abort`

```json
{ "error_code": 0 }
```

## `/api/mbgate.json`: MODBUS request

Input parameters:

| Parameter  | Value Range                             | Description                                                                                                                                                                                                                                                                                             |
| ---------- | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `request`  | HEX string, up to 255 bytes             | Precomposed MODBUS request data (without checksum, starting from the MODBUS address of the device). The presence of this parameter excludes all other parameters.                                                                                                                                       |
| `uid`      | 0...255                                 | MODBUS device address. If the parameter is not specified, the address of the internal virtual device is selected.                                                                                                                                                                                       |
| `write`    | 0 / 1                                   | Write flag (functions 5, 6, 15 or 16). If the parameter is not specified, it is set to 0 - read (functions 1, 2, 3 or 4).                                                                                                                                                                               |
| `readonly` | 0 / 1                                   | Read flag from binary inputs (function 2) or input registers (function 4). It is incompatible with the `write` parameter enabled. If the parameter is not specified it is set to 0 (functions 1, 3, 5, 6, 15 or 16).                                                                                    |
| `bits`     | 0 / 1                                   | Data bit area flag (functions 1, 2, 5 or 15). If the parameter is not specified, it is taken as 0 - register data area (functions 3, 4, 6 or 16).                                                                                                                                                       |
| `address`  | 0...65535                               | Address of the first element (bit or register). Required.                                                                                                                                                                                                                                               |
| `count`    | 1...124 or 1...1984 (depends on `bits`) | Number of elements (bits or registers). Required parameter for reading (functions 1, 2, 3 or 4) and for writing to multiple elements (functions 15 or 16). Incompatible with the `value` parameter (writing to a single element).                                                                       |
| `value`    | -32768...65535                          | Value for writing to a bit or register. Required parameter for writing to a single element (functions 5 or 6). Incompatible with `count` and `data` parameters (read or write to multiple elements). The value is automatically converted to unsigned 16-bit for registers, or to a bit value (if ≠ 0). |
| `data`     | HEX string, up to 248 bytes             | Data to write to bits or registers. Obligatory with `count` parameter for writing to multiple elements (functions 15 or 16). Incompatible with the `value` parameter (write to a single element).                                                                                                       |

The request parameter can be passed in the url string (if the request method is `GET`) or in the
HTTP payload of the request in the format `x-www-form-urlencoded` (if the request method `POST`).

Response is a JSON object with the following fields:

| Parameter        | Value Range                             | Description                                                                                                                                                                                                                                                                             |
| ---------------- | --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `response`       | HEX string, up to 255 bytes             | Optional. Response to successful precomposed request.                                                                                                                                                                                                                                    |
| `data`           | HEX string, up to 248 bytes             | Optional. Successful read response payload.                                                                                                                                                                                                                                              |
| `count`          | 1...124 or 1...1984 (depends on `bits`) | Optional. Number of written values for successful multiwrite.                                                                                                                                                                                                                            |
| `value`          | 0...65535                               | Optional. Value for successful single write.                                                                                                                                                                                                                                             |
| `exception_code` | 0...255                                 | Optional. MODBUS exception code. May represent an error that is compatible with MODBUS (e.g. request timeout) in that case `error_code` will be empty. Periphery-returned exceptions are indistinguishable from exceptions generated by controller itself during the request processing. |
| `error_code`     | -255...0                                | Optional. Request processing error code.                                                                                                                                                                                                                                                 |
| `error_descr`    | String                                  | Optional. Request processing error description.                                                                                                                                                                                                                                          |

Request processing and the response can take up to 15s (in most cases much faster).

_Examples:_

Read OPCB-221 identification data (device type 42=0x2A, version 1):

- Option 1

`/api/mbgate.json?address=0&count=2`

```json
{ "data": "002A0001" }
```

- Option 2

`/api/mbgate.json?request=6F0300000002`

```json
{ "response": "6F0304002A0001" }
```

Change RS-485 serial port 1 byte format to uneven (code 4):

`/api/mbgate.json?write=1&address=502&value=4`

```json
{ "value": 4 }
```

Change the value of the holding register with address 120 of the device with MODBUS ID 12 (not connected):

`/api/mbgate.json?uid=73&write=1&address=120&value=3`

```json
{ "exception_code": "0B" }
```

Roll-back temporary changes of configuration parameters of OPCB-221 to configuration file values
(command code `0xCF2C`):

`/api/mbgate.json?address=50&count=1&data=CF2C`

```json
{ "count": 1, "error_code": 0 }
```

Call function 0 on OPCB-221 (incorrect function code):

`/api/mbgate.json?request=6F00`

```json
{ "error_code": -2, "error_descr": "Input illegal" }
```
