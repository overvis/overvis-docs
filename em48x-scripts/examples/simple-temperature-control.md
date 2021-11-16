# Simple temperature control based on EM-486

This example program switches the [EM-486](https://www.overvis.com/equipment/em-486/) relay on/off depending on sensor reading. If sensor reading is below minimum value, then relay is turned ON. If sensor reading is above maximum value, then relay is turned OFF. In between - relay state is undefined (not controlled by EM-486).

The program is controlled through Overvis using registers on EM-481 device.

Address | Description
--------|------------
5250    | Lower temperature threshold
5251    | Upper temperature threshold


```bash
!META
* PROTOCOLVERSION 8

!PARAMS
# EM-486 input 1 (connect temperature sensor to input 1)
0 * INT16 H 150
# Setting: Lower temperature threshold - Overvis parameter address 5250
1 * INT16 H 5500
# Setting: Upper temperature threshold - Overvis parameter address 5251
2 * INT16 H 5501
# EM-486 relay 1 state
3 * UINT16 H 160

!VARS
# Temperature sensor value
0 PARAMVAL P0
# Lower temperature threshold setting
1 PARAMVAL P1
# Upper temperature threshold setting
2 PARAMVAL P2
# Relay 1 state
3 PARAMVAL P3

!CONDS
# Temperature value is below lower border
0 VARLSVAR V0 V1
# Temperature value is higher than upper border
1 VARGRVAR V0 V2
# Relay 1 is switched on
2 VAREQVAL V3 1
# Relay 1 is switched off
3 VAREQVAL V3 0
# Relay should be switched on
4 AND C0 C3
# Relay should be switched off
5 AND C1 C2

!ACTS
0 RELAYON 1 1
1 RELAYOFF 1 1

!REACTS
* C4 ACT A0
* C5 ACT A1

# END.
```

This program can easily be modified to control the [OB-215](https://www.overvis.com/equipment/ob-215/) relay with [EM-481](https://www.overvis.com/equipment/em-481/). In that case, parameter `P3` should be changed to relay value of OB-215 and ON/OFF actions (section `!ACTS`) should be changed to OB-215 control register modifications. See [astronomical timer example.](./astronomical-timer.md)

