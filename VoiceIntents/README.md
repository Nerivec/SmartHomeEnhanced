# Voice Intents

# Climate

### Requirements

- Area `Outside`
  - Temperature sensor (at least 1)
- Area `Terrace`
  - Temperature sensor (at least 1)
- Area `Basement`
  - Temperature sensor (at least 1)
  - Humidity sensor (at least 1)
- Area `Kitchen`
  - Temperature sensor (at least 1)
- Area `Living Room`
  - Temperature sensor (at least 1)
- Area `Bedroom`
  - Temperature sensor (at least 1)
- [OPTIONAL] Group `sensor.mean_house_humidity`

NOTE: You can also replace with direct sensor states instead of area device_class lookups.



# Here

Direct (quicker/easier) control of "where you currently are" through device area.

### Requirements

- Text `input_text.last_voice_box_triggered`
  - Used to keep track of last activated device (in the form of the associated media player entity ID) for various needs (like area last triggered, quick volume control, etc)
  - See `VoiceAssist` folder



# Modes

Simple turning on and off of modes that then supposedly trigger automations or alter automations behaviors.

### Requirements

- Toggle `input_boolean.sleep_mode`
  - For intents `SleepModeOn` and `SleepModeOff` if you have a specific automation for sleep on/off

- Toggle `input_boolean.manual_kitchen_mode`
  - For intents `AreaManualModeOn` and `AreaManualModeOff` if you have an automation to temporarily disable motion's effect on an area; for example, deactivating lights turning off while you are cooking (if you are a bit too still!)
  - Can do the same with any area.



# Solar

### Requirements

- `sensor.solar_power_meter_power`
  - A meter clamp sensor at your panels' AC output
- `sensor.power_production_now`
  - [Forecast.Solar](https://www.home-assistant.io/integrations/forecast_solar) integration (added automatically during Energy dashboard configuration, or manually...)



# Time

NOTE: Timer stuff only supports 3 timers (for now, don't need more); if you need more, add more and modify the configuration accordingly. Hopefully Home Assistant will come up with something better for voice control on that front.

### Requirements

#### Timers

- Timer `timer.timer1`
  - Recognized as 'one', 'first', or 'A'
- Timer `timer.timer2`
  - Recognized as 'two', 'second', or 'B'
- Timer `timer.timer3`
  - Recognized as 'three', 'third', or 'C'

#### Alarm

- Datetime (with date + time) `input_datetime.wake_up_alarm_time`
- Automation `automation.wake_up_alarm_time_triggers`



# Weather

### Requirements

- Weather Integration `weather.home`
  - Supporting the following properties (remove portions to match what yours provides):
    - `cloud_coverage`
    - `wind_bearing`
    - `wind_speed`
    - `sensor.home_rain_chance`
    - `sensor.home_snow_chance`
