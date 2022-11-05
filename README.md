# kronaby
[Kronaby watch](https://kronaby.com/) API research

# 1. Why?
I have Kronaby watch and I want to enhance the webhook triggering feature to the absolute maximum. So I can trigger a set of webhooks using any button on the watch, not only one. This will require protocol reverse-ngineering and creating a mobile app.

# 2. Tools and resources

* https://github.com/joakar/kronaby - old, but releavant implementation of kronaby protocol
* [Official Kronaby App](https://play.google.com/store/apps/details?id=com.kronaby.watch.app) - to sniff data from
* [Wireshark](https://www.wireshark.org/)
* [Apple PacketLogger](https://developer.apple.com/bluetooth/) + iphone - because it allows live log viewing. On Android this would require rooting.

# 3. Notes

#### Kronaby watch uses MsgPack https://msgpack.org/ to communicate over BLE.

#### My watch is Apex 43mm. Firmware version is `20180911.01.03` and I found no way to update it.

# 4. Observation

## Watch can send events:

* Top pusher
  * Single short press `0x8108920001`
  * Double short press `0x8108920003`
  * Triple short press `0x8108920004`
  * Quadruple short press `0x8108920005`
  * Long press start `0x8108920002`
  * Long press end `0x810892000C`
  * Single short + Long press start `0x8108920006`
  * Double short press + Long press start `0x8108920007`
  * Triple short press * Long press start `0x8108920008`
* Crown
  * Single short press `0x8108920101`
  * Double short press `0x8108920103`
  * Triple short press `0x8108920104`
  * Quadruple short press `0x81-08-92-01-05`
  * Long press start `0x8108920102`
  * Find my phone `long press for 3 sec` `0x810892010B`
  * Long press end `0x810892010C`
  * Single short + Long press start `0x8108920106`
  * Double short press + Long press start `0x8108920106`
  * Triple short press * Long press start `0x8108920107`
* Bottom pusher
  * Single short press `0x8108920201`
  * Double short press `0x8108920203`
  * Triple short press `0x8108920204`
  * Quadruple short press `0x8108920205`
  * Long press start `0x8108920202`
  * Long press end `0x810892010C`
  * Single short + Long press start `0x8108920006`
  * Double short press + Long press start `0x8108920206`
  * Triple short press * Long press start `0x8108920207` 


# 5. Protocol reference

On application layer protocol supports following commands:

| Code | Command |
| :----: | ------- |
| 0  | [`map_cmd`](#map_cmd) |
| 1  | [`onboarding_done`](#onboarding_done)|
| 2  | [`config_base`](#config_base)|
| 3  | [`status_buildinfo`](#status_buildinfo)|
| 4  | [`status_buildinfo_bl`](#status_buildinfo_bl)|
| 5  | [`status_crash`](#status_crash)|
| 6  | [`status_diag`](#status_diag)|
| 7  | [`cap`](#cap)|
| 8  | [`fastmode`](#fastmode)|
| 9  | [`id_apperror`](#id_apperror)|
| 10 | [`id_error`](#id_error)|
| 11 | [`id_hardfault`](#id_hardfault)|
| 12 | [`id_forced_hardfault`](#id_forced_hardfault)|
| 13 | [`crash`](#crash)|
| 14 | [`map_error`](#map_error)|
| 15 | [`map_buildinfo`](#map_buildinfo)|
| 16 | [`map_diag`](#map_diag)|
| 17 | [`map_settings`](#map_settings)|
| 18 | [`map_diag_event`](#map_diag_event)|
| 19 | [`error`](#error)|
| 20 | [`datetime`](#datetime)|
| 21 | [`weekly_sync`](#weekly_sync)|
| 22 | [`periodic`](#periodic)|
| 23 | [`forget_device`](#forget_device)|
| 24 | [`conn_int_change`](#conn_int_change)|
| 25 | [`rssi`](#rssi)|
| 26 | [`postmortem`](#postmortem)|
| 27 | [`dump_uart`](#dump_uart)|
| 28 | [`settings`](#settings)|
| 29 | [`diag_event`](#diag_event)|
| 30 | [`vbat`](#vbat)|
| 31 | [`vbat_sim`](#vbat_sim)|
| 32 | [`upgrade_occurred`](#upgrade_occurred)|
| 33 | [`button`](#button)|
| 34 | [`triggers`](#triggers)|
| 35 | [`btn_trigger`](#btn_trigger)|
| 36 | [`btn_feature`](#btn_feature)|
| 37 | [`dnd`](#dnd)|
| 38 | [`daylight`](#daylight)|
| 39 | [`auto_edst`](#auto_edst)|
| 40 | [`dfu_ready`](#dfu_ready)|
| 41 | [`config_debug`](#config_debug)|
| 71 | [`command_71`](#command_71)


## `map_cmd`

## `onboarding_done`

*Assumption*: Stops watch hands from continuous movement after pairing and connection.

## `config_base`
*Assumption*: Sends list of 4 values to the watch:

* Time resolution in minutes - integers
	*Assumption*: period between sending steps count from watch to phone
* Enable step counter - boolean
* Unknown1 - boolean/integer
* Unknown2 - boolean/integer

## `status_buildinfo`

## `status_buildinfo_bl`

## `status_crash`

## `status_diag`

## `cap`

## `fastmode`

## `id_apperror`

## `id_error`

## `id_hardfault`

## `id_forced_hardfault`

## `crash`

## `map_error`

## `map_buildinfo`

## `map_diag`

## `map_settings`

## `map_diag_event`

## `error`

## `datetime`

*Confirmed*: Sends date and time to the watch

* Year - YYYY
* month - 1 - 12
* day of month - 1 - 31
* hour 0 - 23
* minutes 0 - 59
* seconds 0 - 59
* day of week - 0 - 6


## `weekly_sync`

## `periodic`

## `forget_device`

Tells watch to unpair from current phone

Example: `811700` - `{23:0}`

## `conn_int_change`

## `rssi`

## `postmortem`

## `dump_uart`

## `settings`

## `diag_event`

## `vbat`

*Assumption*: Request battery voltage

## `vbat_sim`

*Assumption*: Force watch to report specified voltage (**sim**ulate)

## `upgrade_occurred`

## `button`

## `triggers`

## `btn_trigger`

## `btn_feature`

## `dnd`

## `daylight`

## `auto_edst`

## `dfu_ready`

## `config_debug`

## `command_71`

Hand position adjustment

Minute hand clockwise adjust (+1)

`814793000101` - `{ 71: [0,1,1] }`

Minute hand counter-clockwise adjust (-1)

`8147930001ff` - `{ 71:[0,1,-1] }`

Hour hand clockwise adjust (+1)

`814793000001` - `{ 71: [0,1,1] }`

Hour hand counter-clockwise adjust (-1)

`8147930000ff` - `{ 71:[0,1,-1] }`


Right dial hand counter-clockwise adjust

`8147930100ff` - `{ 71: [1,0,-1] }`

Right dial hand clockwise adjust

`814793010001` - `{ 71: [1,0,1] }`


Left dial hand counter-clockwise adjust

`8147930200ff` - `{ 71: [2,0,-1] }`

Left dial hand clockwise adjust

`814793020001` - `{ 71: [2,0,1] }`
