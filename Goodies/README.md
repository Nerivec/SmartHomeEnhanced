# solar_power.yaml

Support grid import and export with 2x one-way meter clamps.
One of the clamp should be after the point where your solar panels get converted to AC, but before any other circuits meet.
The other should be right after the electrical company's tie-in, usually in your house's electrical panel.
Both should be clamped according to your device's specs (in most cases an arrow indicates the proper way). You will usually want to point the panels' one as if it was another grid coming in to your house.

WARNING: This might not work for every grid/device or might need to be tweaked. Be careful when dealing with high voltage sources!

I currently have `input_number.grid_import_export_offset` set to 15 Watts, you might want to adapt the value according to your system.


# weather_warning.yaml

A few template sensors to keep track of bad weather for notifications.

WARNING: Will stop working when Home Assistant enforces the new "get forecast from service" policy. An alternative is to use the 'automation' version that checks periodically for the same thing.

# trends.yaml

[Trend](https://www.home-assistant.io/integrations/trend/) sensors. You don't see them used often, but they allow tailored detection of "direction".

I use these primarily to drive automations that care about the direction a temperature is going, more than the value. Of course, they can be used with any value, not just temperature.

They also make a nice addition to your favorite dashboard:

```yaml
type: markdown
content: >-
  {%- if is_state('binary_sensor.outside_temperature_falling', 'on') %}
    <ha-alert alert-type="error">Outside temperature is falling!</ha-alert>
  {%- elif is_state('binary_sensor.outside_temperature_rising', 'on') %}
    <ha-alert alert-type="success">Outside temperature is rising!</ha-alert>
  {%- endif %}
```

or

```yaml
type: horizontal-stack
cards:
  - type: conditional
    conditions:
      - condition: state
        entity: binary_sensor.outside_temperature_falling
        state: 'on'
    card:
      type: tile
      entity: binary_sensor.outside_temperature_falling
      state_content: last-changed
  - type: conditional
    conditions:
      - condition: state
        entity: binary_sensor.outside_temperature_rising
        state: 'on'
    card:
      type: tile
      entity: binary_sensor.outside_temperature_rising
      state_content: last-changed
```
