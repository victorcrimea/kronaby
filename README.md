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

> **Obscure** - command name appears in the watch's `map_cmd` bootstrap dictionary but has never been observed on BLE traffic.

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
| 10 | [`id_error`](#id_error) *(obscure)*|
| 11 | [`id_hardfault`](#id_hardfault) *(obscure)*|
| 12 | [`id_forced_hardfault`](#id_forced_hardfault) *(obscure)*|
| 13 | [`crash`](#crash)|
| 14 | [`map_error`](#map_error)|
| 15 | [`map_buildinfo`](#map_buildinfo)|
| 16 | [`map_diag`](#map_diag)|
| 17 | [`map_settings`](#map_settings)|
| 18 | [`map_diag_event`](#map_diag_event)|
| 19 | [`error`](#error)|
| 20 | [`datetime`](#datetime)|
| 21 | [`weekly_sync`](#weekly_sync) *(obscure)*|
| 22 | [`periodic`](#periodic) *(obscure)*|
| 23 | [`forget_device`](#forget_device)|
| 24 | [`conn_int_change`](#conn_int_change)|
| 25 | [`rssi`](#rssi)|
| 26 | [`postmortem`](#postmortem)|
| 27 | [`dump_uart`](#dump_uart)|
| 28 | [`settings`](#settings)|
| 29 | [`diag_event`](#diag_event)|
| 30 | [`vbat`](#vbat) *(obscure)*|
| 31 | [`vbat_sim`](#vbat_sim)|
| 32 | [`upgrade_occurred`](#upgrade_occurred) *(obscure)*|
| 33 | [`button`](#button)|
| 34 | [`triggers`](#triggers)|
| 35 | [`btn_trigger`](#btn_trigger) *(obscure)*|
| 36 | [`btn_feature`](#btn_feature) *(obscure)*|
| 37 | [`dnd`](#dnd)|
| 38 | [`daylight`](#daylight) *(obscure)*|
| 39 | [`auto_edst`](#auto_edst) *(obscure)*|
| 40 | [`dfu_ready`](#dfu_ready)|
| 41 | [`config_debug`](#config_debug)|
| 42 | [`debug_hardfault`](#debug_hardfault)|
| 43 | [`debug_apperror`](#debug_apperror)|
| 44 | [`debug_watchdog`](#debug_watchdog) *(obscure)*|
| 45 | [`debug_disconnect`](#debug_disconnect)|
| 46 | [`debug_rssi`](#debug_rssi)|
| 47 | [`test`](#test)|
| 48 | [`test_coil`](#test_coil)|
| 49 | [`test_fcte`](#test_fcte)|
| 50 | [`alarm`](#alarm)|
| 51 | [`alert_assign`](#alert_assign)|
| 52 | [`alert`](#alert)|
| 53 | [`ancs_filter`](#ancs_filter)|
| 54 | [`ancs_misuse`](#ancs_misuse) *(obscure)*|
| 55 | [`ancs_activity`](#ancs_activity) *(obscure)*|
| 56 | [`call`](#call)|
| 57 | [`comp_def`](#comp_def)|
| 58 | [`comp_btn`](#comp_btn)|
| 59 | [`timezone`](#timezone)|
| 60 | [`timezone2`](#timezone2) *(obscure)*|
| 61 | [`timezone3`](#timezone3) *(obscure)*|
| 62 | [`gmt_watch_tz`](#gmt_watch_tz) *(obscure)*|
| 63 | [`remote_data`](#remote_data)|
| 64 | [`remote_data_config`](#remote_data_config)|
| 65 | [`stopwatch`](#stopwatch)|
| 66 | [`newyear`](#newyear) *(obscure)*|
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


## <a name="map_cmd"></a>`map_cmd` - cmd 0

Gets a main map of supported commands. May be more than 1 page.

<details><summary>Examples</summary>
<p>

Page 1 - write `0x92 00 d2 00 00 00 00`

then read from the same charachteristic

</p>
</details>



## <a name="onboarding_done"></a>`onboarding_done` - cmd 1

*Assumption*: Stops watch hands from continuous movement after pairing and connection.

## <a name="config_base"></a>`config_base` - cmd 2
*Assumption*: Sends list of 4 values to the watch:

* Time resolution in minutes - integers
	*Assumption*: period between sending steps count from watch to phone
* Enable step counter - boolean
* Unknown1 - boolean/integer
* Unknown2 - boolean/integer

## <a name="status_buildinfo"></a>`status_buildinfo` - cmd 3

## <a name="status_buildinfo_bl"></a>`status_buildinfo_bl` - cmd 4

## <a name="status_crash"></a>`status_crash` - cmd 5

## <a name="status_diag"></a>`status_diag` - cmd 6

## <a name="cap"></a>`cap` - cmd 7

## <a name="fastmode"></a>`fastmode` - cmd 8

## <a name="id_apperror"></a>`id_apperror` - cmd 9

## <a name="id_error"></a>`id_error` - cmd 10 *(obscure)*

## <a name="id_hardfault"></a>`id_hardfault` - cmd 11 *(obscure)*

## <a name="id_forced_hardfault"></a>`id_forced_hardfault` - cmd 12 *(obscure)*

## <a name="crash"></a>`crash` - cmd 13

## <a name="map_error"></a>`map_error` - cmd 14

Maps error field IDs to their names.

| ID | Field |
| :--: | ----- |
| 129 | `line_number` |
| 130 | `file_name` |
| 131 | `error_code` |
| 132 | `r0` |
| 133 | `r1` |
| 134 | `r2` |
| 135 | `r3` |
| 136 | `r12` |
| 137 | `sp` |
| 138 | `lr` |
| 139 | `pc` |
| 140 | `psr` |

## <a name="map_buildinfo"></a>`map_buildinfo` - cmd 15

Maps build info field IDs to their names.

| ID | Field |
| :--: | ----- |
| 79 | `builder` |
| 80 | `fw_version` |
| 81 | `fw_type` |
| 82 | `build_date` |
| 83 | `device_name` |

## <a name="map_diag"></a>`map_diag` - cmd 16

Maps diagnostic field IDs to their names.

| ID | Field |
| :--: | ----- |
| 84 | `uptime_in_min` |
| 85 | `runlevel` |
| 86 | `num_of_button_presses` |
| 87 | `num_of_button_events` |
| 88 | `has_crashed` |
| 89 | `num_of_crashes` |
| 90 | `num_of_vibrator_starts` |
| 91 | `sw_reset_reason` |
| 92 | `hw_reset_reason` |
| 93 | `num_of_failed_conn_int` |
| 94 | `min_conn_interval` |
| 95 | `max_conn_interval` |
| 96 | `num_of_factory_resets` |
| 97 | `battery_state` |
| 98 | `batt_load_hourly_mv` |
| 99 | `batt_load_daily_mv` |
| 100 | `batt_load_avg_mv` |
| 101 | `num_of_advertisement_periods` |
| 102 | `time_compensation` |
| 103 | `num_of_reboots` |
| 104 | `task_watermark` |
| 105 | `ci_integral_ever` |
| 106 | `num_of_connections` |
| 107 | `ci_time` |
| 108 | `num_of_disconnects` |
| 109 | `num_of_still_events` |
| 110 | `num_of_get_up_events` |
| 111 | `num_of_watch_unused` |
| 112 | `num_of_repairings` |
| 113 | `connection_state_data` |
| 114 | `ticks_executed` |
| 115 | `num_of_wakeups` |
| 116 | `num_of_alarm_events` |
| 117 | `num_of_ancs_notifications_today` |
| 118 | `num_of_complication_uses` |
| 119 | `max_complicationmgr_stack_depth` |
| 120 | `num_of_acc_irqs` |
| 121 | `num_of_accel_samples` |
| 122 | `accel_active` |
| 123 | `vibrator_on_in_ms` |
| 124 | `batt_vib_min_mv` |
| 125 | `batt_vib_min_temp` |
| 126 | `batt_vib_latest_mv` |

## <a name="map_settings"></a>`map_settings` - cmd 17

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

## <a name="map_diag_event"></a>`map_diag_event` - cmd 18

## <a name="error"></a>`error` - cmd 19

Watch-initiated notification sent when a command fails. Payload is a single integer error code.

`{19: <error_code>}`


| Code | Name |
| ---: | ---- |
| 0  | `NO_ERROR` |
| 1  | `WRONG_NUMBER_OF_ARGS` |
| 2  | `INVALID_ARGUMENT` |
| 3  | `FAULTY_MSGPACK_FORMAT` |
| 4  | `TEMPORARILY_OUT_OF_MEM_TRY_AGAIN` |
| 5  | `BATTERY_CRITICAL` |
| 6  | `BATTERY_WARNING` |
| 7  | `BUFFER_TOO_SMALL` |
| 8  | `STACK_OVERFLOW` |
| 9  | `VIBRATOR_TEMP_OUT_OF_RANGE` |
| 10 | `UNKNOWN_TESTCASE` |
| 11 | `ON_STARTUP` |
| 12 | `ANCS_FILTER_OVERFLOW` |
| 13 | `DISPLAY_PARTIAL_LINE_RECEIVED` |
| 14 | `DISPLAY_TOO_MUCH_DATA` |
| 15 | `DISPLAY_FEED_NOT_STARTED` |
| 16 | `DISPLAY_TRANSFER_ONGOING` |
| 17 | `CALIBRATION_TIMEOUT` |
| 18 | `CALIBRATION_INVALID_STATE` |
| 19 | `POSTMORTEM_FLASH_FAILED` |
| 20 | `INTERNAL` |
| 21 | `POSTMORTEM_INVALID_STATE` |
| 22 | `BATTERY_OK` |
| 23 | `UNHANDLED_COMMAND` |

## <a name="datetime"></a>`datetime` - cmd 20

*Confirmed*: Sends date and time to the watch

* Year - YYYY
* month - 1 - 12
* day of month - 1 - 31
* hour 0 - 23
* minutes 0 - 59
* seconds 0 - 59
* day of week - 0 - 6


## <a name="weekly_sync"></a>`weekly_sync` - cmd 21 *(obscure)*

## <a name="periodic"></a>`periodic` - cmd 22 *(obscure)*

## <a name="forget_device"></a>`forget_device` - cmd 23

Tells watch to unpair from current phone

Example: `811700` - `{23:0}`

## <a name="conn_int_change"></a>`conn_int_change` - cmd 24

## <a name="rssi"></a>`rssi` - cmd 25

Ask watch to report it's RSSI reading.

<details><summary>Examples</summary>
<p>

Ask - write `0xd2 00 00 00 19`

then read from the same charachteristic, example: `81 19 d0 db` -37dB

</p>
</details>

## <a name="postmortem"></a>`postmortem` - cmd 26

## <a name="dump_uart"></a>`dump_uart` - cmd 27

## <a name="settings"></a>`settings` - cmd 28

## <a name="diag_event"></a>`diag_event` - cmd 29

## <a name="vbat"></a>`vbat` - cmd 30 *(obscure)*

*Assumption*: Request battery voltage

## <a name="vbat_sim"></a>`vbat_sim` - cmd 31

*Assumption*: Force watch to report specified voltage (**sim**ulate)

## <a name="upgrade_occurred"></a>`upgrade_occurred` - cmd 32 *(obscure)*

## <a name="button"></a>`button` - cmd 33

## <a name="triggers"></a>`triggers` - cmd 34

Sent before removing a button's program assignment. Always observed as `{34: [0,0]}` regardless of which button is being cleared. Likely resets face-level trigger state before the `comp_btn` unassign step.

| Arg | Meaning | Observed values |
| --- | ------- | --------------- |
| 0 | unknown | 0 |
| 1 | unknown | 0 |

## <a name="btn_trigger"></a>`btn_trigger` - cmd 35 *(obscure)*

## <a name="btn_feature"></a>`btn_feature` - cmd 36 *(obscure)*

## <a name="dnd"></a>`dnd` - cmd 37

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

## <a name="daylight"></a>`daylight` - cmd 38 *(obscure)*

## <a name="auto_edst"></a>`auto_edst` - cmd 39 *(obscure)*

## <a name="dfu_ready"></a>`dfu_ready` - cmd 40

## <a name="config_debug"></a>`config_debug` - cmd 41

## <a name="debug_hardfault"></a>`debug_hardfault` - cmd 42

## <a name="debug_apperror"></a>`debug_apperror` - cmd 43

## <a name="debug_watchdog"></a>`debug_watchdog` - cmd 44 *(obscure)*

## <a name="debug_disconnect"></a>`debug_disconnect` - cmd 45

## <a name="debug_rssi"></a>`debug_rssi` - cmd 46

## <a name="test"></a>`test` - cmd 47

## <a name="test_coil"></a>`test_coil` - cmd 48

## <a name="test_fcte"></a>`test_fcte` - cmd 49

## <a name="alarm"></a>`alarm` - cmd 50

## <a name="alert_assign"></a>`alert_assign` - cmd 51

Assigns notification types to vibration pattern slots. Phone → watch (SET only). Requires firmware >= 20170124.01.

Payload: `{51: [bitmask_slot1, bitmask_slot2, bitmask_slot3]}` - or 6 elements on watches where `cap` field `0x11` is true.

Each array element is a bitmask for one slot - the OR of all notification types assigned to it:

| Bit | Value | Notification type |
| --- | ----- | ----------------- |
| 0 | `0x01` | Alarm |
| 1 | `0x02` | Stillness reminder |
| 2 | `0x04` | Step goal reached |
| 3 | `0x08` | Incoming calls *(only when `cap` field `0x09` `CAP_SW_CALL_REPEATS_ALERT` is true)* |
| 4 | `0x10` | Phone disconnect *(only when `cap` field `0x1b` `CAP_SW_DISCONNECT_ALERT` is true)* |

Each notification type points to exactly one slot (1–6). A slot's bitmask is 0 if no notification types are assigned to it. Value 0 for a notification type's slot means unassigned/disabled.

**Example** - alarm on slot 1, stillness on slot 2, step goal on slot 3, nothing on remaining slots:

`{51: [0x01, 0x02, 0x04]}`

## <a name="alert"></a>`alert` - cmd 52

Triggers a vibration + hand movement on the watch. Phone → watch (SET only).

Payload: `{52: N}` where N is an alert ID from the `Alert` enum.

Source: `com.animaconnected.watch.device.Alert` enum.

| ID | Name | Usage |
| --: | ---- | ----- |
| 1 | `Pattern1` | Notification slot 1 vibration |
| 2 | `Pattern2` | Notification slot 2 vibration |
| 3 | `Pattern3` | Notification slot 3 vibration |
| 4 | `Pattern4` | Notification slot 4 *(only on `hasSixAlerts` watches)* |
| 5 | `Pattern5` | Notification slot 5 *(only on `hasSixAlerts` watches)* |
| 6 | `Pattern6` | Notification slot 6 *(only on `hasSixAlerts` watches)* |
| 7 | `Confirm` | Confirmation vibration (IFTTT trigger, camera shutter) |
| 8 | `Failed` | Failure vibration *(present in enum, no observed call site)* |

IDs 1–6 correspond directly to the slot indices from `alert_assign`. The app sends the slot ID that matches the arriving notification type's assignment.

**Incoming call** repeats the assigned alert every 3 seconds for up to 90 seconds (30 repeats).

## <a name="ancs_filter"></a>`ancs_filter` - cmd 53

## <a name="ancs_misuse"></a>`ancs_misuse` - cmd 54 *(obscure)*

## <a name="ancs_activity"></a>`ancs_activity` - cmd 55 *(obscure)*

## <a name="call"></a>`call` - cmd 56

## <a name="comp_def"></a>`comp_def` - cmd 57

## <a name="comp_btn"></a>`comp_btn` - cmd 58

Assigns or removes a complication (program) from a button gesture on a specific dial face.

Arguments: `[button_id, button_action_id, watch_face_id, internal_program_id]`

**button_id** - physical button:

| Value | Button |
| ----- | ------ |
| 0x00 | Top pusher |
| 0x01 | Crown |
| 0x02 | Bottom pusher |

**button_action_id** - gesture on that button. Reuses the same action code as in BLE input event notifications (byte[3] of `0x810892...`):

| Value | Gesture |
| ----- | ------- |
| 1 | Single short press |
| 2 | Long press |
| 3 | Double short press |
| 4 | Triple short press |

**watch_face_id** - which dial face:

| Value | Face |
| ----- | ---- |
| 0 | Main dial |
| 1 | First subdial |
| 2 | Second subdial |

> Only `watch_face_id=0` confirmed working. Sending `1` returns `{error: 2}` (`INVALID_ARGUMENT`) - subdial complication assignment may not be supported on this model (A1000).

**internal_program_id** - complication to assign:

| Value | Program |
| ----- | ------- |
| 0 | Date |
| 4 | Daily Hundred |
| 15 | *(unassign - removes current program)* |

**Remove sequence:** always preceded by `{34: [0,0]}` (triggers reset), then `comp_btn` with `internal_program_id=15`.

<details><summary>Examples</summary>
<p>

Assign Date to Crown X1 (single press, main dial):
`{58: [1, 1, 0, 0]}`

Assign Daily Hundred to Crown X1:
`{58: [1, 1, 0, 4]}`

Assign Date to Crown X2 (double press, main dial):
`{58: [1, 3, 0, 0]}`

Remove Crown X1 program:
`{34: [0, 0]}` → `{58: [1, 1, 0, 15]}`

Remove Crown X2 program:
`{34: [0, 0]}` → `{58: [1, 3, 0, 15]}`

</p>
</details>

## <a name="timezone"></a>`timezone` - cmd 59

## <a name="timezone2"></a>`timezone2` - cmd 60 *(obscure)*

## <a name="timezone3"></a>`timezone3` - cmd 61 *(obscure)*

## <a name="gmt_watch_tz"></a>`gmt_watch_tz` - cmd 62 *(obscure)*

## <a name="remote_data"></a>`remote_data` - cmd 63

## <a name="remote_data_config"></a>`remote_data_config` - cmd 64

## <a name="stopwatch"></a>`stopwatch` - cmd 65

## <a name="newyear"></a>`newyear` - cmd 66 *(obscure)*

## <a name="dice"></a>`dice` - cmd 67

Configures the number of faces on the firmware-side dice complication. The dice rolls entirely on the watch, the app never sends this command.

**Read face count:** write `{67: [0, 0]}` (paged GET), then read back response is `{67: N}`.

**Set face count:** write `{67: N}`  subsequent dice rolls produce values in range 1–N.

| N | Die |
|--:|-----|
| 2 | d2 (coin flip) |
| 4 | d4 |
| 6 | d6 (default) |
| 8 | d8 |
| 10 | d10 |
| 12 | d12 |
| 20 | d20 |

<details><summary>Examples</summary>
<p>

Read current face count, write then read back:
`rw('92 43 d2 00 00 00 00')` -> `81 43 06` = `{67: 6}` (default d6)

Set to d2 (coin flip):
`81 43 02`

Set to d12:
`81 43 0c`

</p>
</details>

## <a name="stepper_goto"></a>`stepper_goto` - cmd 68

Moves a watch hand to an arbitrary position. Movement is temporary (~3 sec), then the hand returns to its current time position.

Arguments: `[hand_id, minutes]`

**hand_id** - which hand to move (watch-model dependent):

| Value | Hand |
| ----- | ---- |
| 0 | Main dial hour hand |
| 1 | Main dial minute hand |
| 2 | First subdial hand |
| 3 | Second subdial hand |

**minutes** - target position on the dial, 0-59. Maps directly to minute marks: 0 = 12 o'clock, 15 = 3 o'clock, 30 = 6 o'clock, 35 = 7 o'clock, 45 = 9 o'clock.

<details><summary>Examples</summary>
<p>

Move hour hand to 12:
`rw('81 44 92 00 00')` = `{68: [0, 0]}`

Move hour hand to 7:
`rw('81 44 92 00 23')` = `{68: [0, 35]}`

Move minute hand to 6:
`rw('81 44 92 01 1e')` = `{68: [1, 30]}`

</p>
</details>

## <a name="recalibrate"></a>`recalibrate` - cmd 69

## <a name="recalibrate_move"></a>`recalibrate_move` - cmd 70

## <a name="recalibrate_hand"></a>`recalibrate_hand` - cmd 71

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

## <a name="steps_target"></a>`steps_target` - cmd 72

Daily step goal. Phone -> watch (SET only). `{72: N}` where `N` is the target step count (e.g. `10000`).

## <a name="stillness"></a>`stillness` - cmd 73

**"Get moving"** reminder: watch vibrates if you haven't moved during your active day hours. Bidirectional. Phone configures it, watch reports state changes.

**Phone -> Watch (configure):** `{73: [timeout, window, start, end]}`

| Arg | Name | Meaning | Default |
| --- | ---- | ------- | ------- |
| 0 | `timeout` | Minutes of inactivity before the reminder fires | `60` |
| 1 | `window` | Detection window in minutes - movement within this window resets the timer | `10` |
| 2 | `start` | Active day start hour (0–23) | `8` → 08:00 |
| 3 | `end` | Active day end hour (0–23) | `19` → 19:00 |

**Disable:** `{73: [0, 0, 0, 0]}`

**Watch -> Phone (state notification):** `{73: N}` - single integer, triggers a phone system notification:

| N | Meaning | Phone action |
|--:|---------|-------------|
| 0 | You moved / period ended | Dismiss notification |
| 1 | You moved after being reminded | Show "moved" (congratulatory) notification |
| 2 | Still too long - time to move | Show "Get moving!" reminder notification |

<details><summary>Examples</summary>
<p>

Configure - remind after 1 hour of stillness, active 08:00–19:00:

`w('81 49 94 3c 0a 08 13')` = `{73: [60, 10, 8, 19]}`

Disable:

`w('81 49 94 00 00 00 00')` = `{73: [0, 0, 0, 0]}`

</p>
</details>

## <a name="steps_now"></a>`steps_now` - cmd 74

Step count notification. Watch -> phone only (unsolicited, not a response to a read). `{74: [steps, day_of_month]}`

| Arg | Meaning |
| --- | ------- |
| 0 | `steps` - step count so far today |
| 1 | `day_of_month` - day of month (1-31); app ignores the update if it doesn't match today |

Example: `{74: [497, 1]}` - 497 steps, reported on the 1st of the month.

## <a name="steps_day"></a>`steps_day` - cmd 75

Bidirectional. Read and write step count for a given day.

**Phone -> watch (read request):** `{75: [day_of_month]}`

**Watch -> phone (response):** `{75: [steps, day_of_month]}`

**Phone -> watch (write):** `{75: [steps, day_of_month]}` - sets the stored step count. App uses this to clear the counter: `{75: [0, day_of_month]}`.

## <a name="vibrator_start"></a>`vibrator_start` - cmd 76

Phone -> watch (SET only). Triggers vibration immediately.

Two forms:

**Built-in test pattern:** `{76: [0]}` - single-element array with value 0. The firmware plays its pre-programmed sequence.

**Custom pattern:** `{76: [on_ms, off_ms, on_ms, ...]}` - alternating on/off durations in milliseconds.

Custom pattern constraints (enforced client-side before sending):
* Length must be odd, from 1 to 17
* First element (on_ms) must be nonzero
* Elements alternate: on-time, off-time, on-time, ...


<details><summary>Examples</summary>
<p>

Built-in test pattern:

`w('81 4c 91 00')`

Single 150 ms buzz:

`w('81 4c 91 cc 96')`

Double buzz [125, 300, 125]:

`w('81 4c 93 7d cd 01 2c 7d')`

Triple buzz [125, 300, 125, 300, 125]:

`w('81 4c 95 7d cd 01 2c 7d cd 01 2c 7d')`

</p>
</details>

## <a name="vibrator_end"></a>`vibrator_end` - cmd 77

Phone -> watch (SET only). Stops an ongoing vibration.

`{77: nil}` - no arguments.

*Assumption*


<details><summary>Examples</summary>
<p>

`w('81 4d c0')`

</p>
</details>

## <a name="vibrator_config"></a>`vibrator_config` - cmd 78

Programs a vibration timing pattern into one firmware slot. Phone -> watch (SET only).

The app sends this command **3 times consecutively** - once per pattern slot (One, Two, Three) - during connection setup or when the "stronger vibrations" setting is toggled.

**Format:** `{78: [slot_id, on_ms, ...]}`

The first element is the slot ID. Remaining elements are the timing pattern (same alternating on/off format as `vibrator_start`).

| Slot | ID |
| ---- | -- |
| One (single buzz) | 8 |
| Two (double buzz) | 9 |
| Three (triple buzz) | 10 |

The pattern content depends on the vibrator motor type.

**Normal vs Stronger array length**

The motor has no power control - firmware can only switch it fully on or off. Normal strength is achieved through rapid on/off pulsing (PWM-style) to reduce average energy delivered. This requires many short elements (e.g. `[50, 25, 80, 25, 35, ...]` where 25 ms gaps are too short to feel as separate pulses). Stronger strength just runs the motor continuously per pulse (e.g. `[600]`), which needs far fewer elements. The 250-300 ms gaps in Stronger patterns are long enough to feel as distinct beats; the 25 ms gaps in Normal patterns are not.

---

**DeviceType BT001 / BT002 / BT003 (Kronaby Apex) - Normal:**

| Slot | Pattern (ms) |
| ---- | ------------ |
| One | `[8, 50, 25, 80, 25, 35, 25, 35, 25, 40, 25, 90]` |
| Two | `[9, 31, 30, 61, 30, 110, 300, 31, 30, 61, 30, 110]` |
| Three | `[10, 31, 30, 190, 300, 50, 30, 90, 300, 50, 30, 90]` |

**DeviceType BT001 / BT002 / BT003 (Kronaby Apex) - Stronger:**

| Slot | Pattern (ms) |
| ---- | ------------ |
| One | `[8, 600]` |
| Two | `[9, 400, 300, 400]` |
| Three | `[10, 330, 250, 330, 250, 330]` |

<details><summary>Captured examples (Apex 43mm, BT003)</summary>
<p>

Normal - Slot One:

`81 4e 9c 08 32 19 50 19 23 19 23 19 28 19 5a`

Normal - Slot Two:

`81 4e 9c 09 1f 1e 3d 1e 6e d1 01 2c 1f 1e 3d 1e 6e`

Normal - Slot Three:

`81 4e 9c 0a 1f 1e cc be d1 01 2c 32 1e 5a d1 01 2c 32 1e 5a`

Stronger - Slot One:

`81 4e 92 08 d1 02 58`

Stronger - Slot Two:

`81 4e 94 09 d1 01 90 d1 01 2c d1 01 90`

Stronger - Slot Three:

`81 4e 96 0a d1 01 4a cc fa d1 01 4a cc fa d1 01 4a`

</p>
</details>


