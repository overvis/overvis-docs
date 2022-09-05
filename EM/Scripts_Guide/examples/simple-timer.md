# Simple timer-based relay control using EM-486

This program example switches the [EM-486's](https://www.overvis.com/equipment/em-486/) relay on/off depending on time of day. At the specified times of the day it switches the relay OFF and ON. Required condition is that OFF-time should be before (less than) ON-time.

The program is controlled through Overvis using registers on EM-481 device.

Address | Description
--------|------------
5250    | OFF time of day in seconds (e.g. 12:34 is 12x3600+34x60=45240).
5251    | ON time of day in seconds.


```bash
!META
* PROTOCOLVERSION 8

!PARAMS
# Current time of day in seconds (EM-486 register)
0 * INT32 H 230
# Setting: Time to turn relay OFF, in seconds
1 * INT32 H 5500
# Setting: Time to turn relay ON, in seconds
2 * INT32 H 5502
# EM-486 relay 1 state
3 * UINT16 H 160

!VARS
# Current time of day in seconds
0 PARAMVAL P0
# Time to turn relay OFF, in seconds
1 PARAMVAL P1
# Time to turn relay ON, in seconds
2 PARAMVAL P2
# EM-486 relay 1 state
3 PARAMVAL P3

!CONDS
# Time of day has already reached switching off time today
0 VARGEVAR V0 V1
# Time of day hasn't yet reached switching on time today
1 VARLSVAR V0 V2
# Relay 2 is switched on
2 VAREQVAL V3 1
# Relay 2 is switched off
3 VAREQVAL V3 0
# It's time to switch the relay OFF
4 AND C0 C1
# It's time to switch the relay ON
5 NOT C4
# Relay should be switched ON
6 AND C3 C5
# Relay should be switched OFF
7 AND C2 C4

!ACTS
0 RELAYON 1 1
1 RELAYOFF 1 1

!REACTS
* C6 ACT A0
* C7 ACT A1

# END.
```

This program can be modified to control the [OB-215](https://www.overvis.com/equipment/ob-215/) relay with [EM-481](https://www.overvis.com/equipment/em-481/). In that case, parameter `P3` should be changed to the relay register of OB-215 and ON/OFF actions (section `!ACTS`) should be changed to the OB-215 control register command. See [astronomical timer example.](./astronomical-timer.md)

