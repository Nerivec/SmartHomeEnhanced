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
