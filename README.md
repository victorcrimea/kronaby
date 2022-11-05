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

* [`map_cmd`](#map_cmd)
* [`onboarding_done`](#onboarding_done)
* [`config_base`](#config_base)
* [`status_buildinfo`](#status_buildinfo)
* [`status_buildinfo_bl`](#status_buildinfo_bl)
* [`status_crash`](#status_crash)
* [`status_diag`](#status_diag)
* [`cap`](#cap)
* [`fastmode`](#fastmode)
* [`id_apperror`](#id_apperror)
* [`id_error`](#id_error)
* [`id_hardfault`](#id_hardfault)
* [`id_forced_hardfault`](#id_forced_hardfault)
* [`crash`](#crash)
* [`map_error`](#map_error)
* [`map_buildinfo`](#map_buildinfo)
* [`map_diag`](#map_diag)
* [`map_settings`](#map_settings)
* [`map_diag_event`](#map_diag_event)
* [`error`](#error)
* [`datetime`](#datetime)
* [`weekly_sync`](#weekly_sync)
* [`periodic`](#periodic)
* [`forget_device`](#forget_device)
* [`conn_int_change`](#conn_int_change)
* [`rssi`](#rssi)
* [`postmortem`](#postmortem)
* [`dump_uart`](#dump_uart)
* [`settings`](#settings)
* [`diag_event`](#diag_event)
* [`vbat`](#vbat)
* [`vbat_sim`](#vbat_sim)
* [`upgrade_occurred`](#upgrade_occurred)
* [`button`](#button)
* [`triggers`](#triggers)
* [`btn_trigger`](#btn_trigger)
* [`btn_feature`](#btn_feature)
* [`dnd`](#dnd)
* [`daylight`](#daylight)
* [`auto_edst`](#auto_edst)
* [`dfu_ready`](#dfu_ready)
* [`config_debug`](#config_debug)


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

*Assumption*: tells watch to unpair from current phone

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
