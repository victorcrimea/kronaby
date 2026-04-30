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
| 42 | [`debug_hardfault`](#debug_hardfault)|
| 43 | [`debug_apperror`](#debug_apperror)|
| 44 | [`debug_watchdog`](#debug_watchdog)|
| 45 | [`debug_disconnect`](#debug_disconnect)|
| 46 | [`debug_rssi`](#debug_rssi)|
| 47 | [`test`](#test)|
| 48 | [`test_coil`](#test_coil)|
| 49 | [`test_fcte`](#test_fcte)|
| 50 | [`alarm`](#alarm)|
| 51 | [`alert_assign`](#alert_assign)|
| 52 | [`alert`](#alert)|
| 53 | [`ancs_filter`](#ancs_filter)|
| 54 | [`ancs_misuse`](#ancs_misuse)|
| 55 | [`ancs_activity`](#ancs_activity)|
| 56 | [`call`](#call)|
| 57 | [`comp_def`](#comp_def)|
| 58 | [`comp_btn`](#comp_btn)|
| 59 | [`timezone`](#timezone)|
| 60 | [`timezone2`](#timezone2)|
| 61 | [`timezone3`](#timezone3)|
| 62 | [`gmt_watch_tz`](#gmt_watch_tz)|
| 63 | [`remote_data`](#remote_data)|
| 64 | [`remote_data_config`](#remote_data_config)|
| 65 | [`stopwatch`](#stopwatch)|
| 66 | [`newyear`](#newyear)|
| 67 | [`dice`](#dice)|
| 68 | [`stepper_goto`](#stepper_goto)|
| 69 | [`recalibrate`](#recalibrate)|
| 70 | [`recalibrate_move`](#recalibrate_move)|
| 71 | [`recalibrate_hand`](#recalibrate_hand)|
| 72 | [`steps_target`](#steps_target)|
| 73 | [`stillness`](#stillness)|
| 74 | [`steps_now`](#steps_now)|
| 75 | [`steps_day`](#steps_day)|
| 76 | [`vibrator_start`](#vibrator_start)|
| 77 | [`vibrator_end`](#vibrator_end)|
| 78 | [`vibrator_config`](#vibrator_config)|


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

Maps build info field IDs to their names.

| ID | Field |
| :--: | ----- |
| 79 | `builder` |
| 80 | `fw_version` |
| 81 | `fw_type` |
| 82 | `build_date` |
| 83 | `device_name` |

## `map_diag`

## `map_settings`

Maps setting IDs to their names.

| ID | Setting |
| :--: | ------- |
| 141 | `battery_load_low_level` |
| 142 | `battery_load_stop_level` |
| 143 | `adv_fastest_timeout` |
| 144 | `adv_fast_timeout` |
| 145 | `adv_slow_timeout` |
| 146 | `adv_fastest_interval` |
| 147 | `adv_fast_interval` |
| 148 | `adv_slow_interval` |
| 149 | `skip_adv_after_minutes_motionless` |
| 150 | `fastmode_timeout` |
| 151 | `notify_timeout` |
| 152 | `bt_conn_int_slow_min` |
| 153 | `bt_conn_int_slow_max` |
| 154 | `bt_conn_int_medium_min` |
| 155 | `bt_conn_int_medium_max` |
| 156 | `bt_conn_int_fast_min` |
| 157 | `bt_conn_int_fast_max` |
| 158 | `slave_latency` |
| 159 | `forcecrash_enabled` |
| 160 | `crash_on_error_enabled` |
| 161 | `dfu_minimum_temperature` |
| 162 | `factory_reset_timeout_ms` |
| 163 | `button_active_timeout_ms` |
| 164 | `long_press_timeout_ms` |
| 165 | `ultra_long_press_timeout` |
| 166 | `button_nth_press_timeout_ms` |
| 167 | `trigger_volume_repeat_ms` |
| 168 | `maximum_temperature` |
| 169 | `minimum_temperature` |
| 170 | `loglevel` |
| 171 | `alarm_active_count` |
| 172 | `alarm_active_time` |
| 173 | `alarm_retry_time` |
| 174 | `alarm_snooze_time` |
| 175 | `alarm_max_retries` |
| 176 | `alert_vibration_enabled` |
| 177 | `alert_complication_enabled` |
| 178 | `alert_exec_num_repeats` |
| 179 | `alert_exec_delta_ms` |
| 180 | `disconnect_vibrate_timeout` |
| 181 | `maximum_ancs_notifications_per_day` |
| 182 | `call_mute_enabled` |
| 183 | `complication_alt_mode_timeout` |
| 184 | `complication_steps_average_days` |
| 185 | `complication_alert_timeout` |
| 186 | `complication_stopwatch_pause_len` |
| 187 | `complication_stopwatch_timeout` |
| 188 | `stepper_recalibration_timeout` |
| 189 | `accel_thresh_act` |
| 190 | `accel_thresh_inact` |
| 191 | `accel_time_inact_s` |
| 192 | `stepcounter_peak_thresh` |
| 193 | `stepcounter_min_ptp` |
| 194 | `stepcounter_max_ptp` |
| 195 | `stepcounter_min_peak_width` |
| 196 | `stepcounter_max_peak_width` |
| 197 | `stepcounter_first_step_hold_off` |
| 198 | `stillness_steps_threshold` |
| 199 | `stillness_attempts_in_complete_motionless` |
| 200 | `nfc_detect_startup_delay_ms` |
| 201 | `battery_nfc_boost_low_level` |
| 202 | `battery_vib_low_level` |
| 203 | `battery_vib_critical_level` |
| 204 | `vibrator_ramp_up_ms` |
| 205 | `vibrator_ramp_down_ms` |
| 206 | `battery_iso7816_low_level` |

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

Disable notifications in specified period

* Argument 1: on/off
* Argement 2: DND start hour
* Argument 3: DND start minute
* Argument 4: DND end hour
* Argument 5: DND end minute

 <details><summary>Examples</summary>
<p>

Set DND 22:00 - 06:00
`8125950116000600` - `{37:[1,22,0,6,0]}`

Set DND 23:00 - 06:00
`8125950117000600` - `{37:[1,23,0,6,0]}`


Set DND 23:00 - 06:05
`8125950117000605` - `{37:[1,23,0,6,5]}`

Unset DND 23:00 - 06:05
`8125950017000605` - `{37:[0,23,0,6,5]}`

</p>
</details>

## `daylight`

## `auto_edst`

## `dfu_ready`

## `config_debug`

## `debug_hardfault`

## `debug_apperror`

## `debug_watchdog`

## `debug_disconnect`

## `debug_rssi`

## `test`

## `test_coil`

## `test_fcte`

## `alarm`

## `alert_assign`

## `alert`

## `ancs_filter`

## `ancs_misuse`

## `ancs_activity`

## `call`

## `comp_def`

## `comp_btn`

## `timezone`

## `timezone2`

## `timezone3`

## `gmt_watch_tz`

## `remote_data`

## `remote_data_config`

## `stopwatch`

## `newyear`

## `dice`

## `stepper_goto`

## `recalibrate`

## `recalibrate_move`

## `recalibrate_hand`

Hand position adjustment.

* Argument 1: dial number, range [0:2]
  * 0 - main dial
  * 1 - right dial on Apex
  * 2 - left dial on Apex
* Argument 2: hand number, range [0:1]
  * 0 - hour hand (or sole hand if dial has only one)
  * 1 - minute hand
* Argument 3: adjustment value (seen values in range [-6:6])
  * positive - clockwise
  * negative counter-clockwise

<details><summary>Examples</summary>
<p>

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

</p>
</details>

## `steps_target`

## `stillness`

## `steps_now`

## `steps_day`

## `vibrator_start`

## `vibrator_end`

## `vibrator_config`

