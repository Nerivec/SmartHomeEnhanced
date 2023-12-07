# Voice Assist

## Configuration

This is for an Intel NUC with Home Assistant OS, and as such is not as limited as RPI hardware-wise.

#### piper

```yaml
voice: en_GB-alba-medium
speaker: 0
length_scale: 1.05
noise_scale: 0.6
noise_w: 0.3
max_piper_procs: 2
debug_logging: false
update_voices: true
```

#### whisper

Fast and pretty accurate, but should really be limited to "quieter" environments.
```yaml
model: base
language: en
beam_size: 5
```

Slower but much better accuracy, especially in noisier environments (like music playing nearby).
```yaml
model: small-int8
language: en
beam_size: 5
```

#### porcupine

```yaml
sensitivity: 0.5
debug_logging: false
```

If you are using media players exposed to Home Assistant for music & the likes, you can simply pause/mute at will whenever a wake word is detected in the area. Wake word detection is much more tolerant of noise than the following STT; this is an easy way to solve the problem. You can do the same whenever TTS is playing if you don't want to drown it in the surrounding noises.

NOTE: You can use remote control integrations (simple keypress support is enough) to mute/lower volume on computers too; when `Assist in progress` triggers from within Home Assistant. Though I'll admit, there is a lack of available options on that front; I found [System Bridge](https://www.home-assistant.io/integrations/system_bridge/) far too bulky for a simple keypress need.


### Common Helpers

- Text `input_text.last_voice_box_triggered`
  - Used to keep track of last activated device (in the form of the associated media player entity ID) for various needs (like area last triggered, quick volume control, etc)
  - Set by ESPHome in `on_stt_end` (should also be set by automation if using mic plugged directly into HA)

# scripts/notify.yaml

An all-in-one notification script for TTS!

Notifications will be queued with a simple `wait_template` whenever any of the targets is already `playing`. WARNING: This might cause trouble if you are using any of the targets for anything other than TTS, it would wait until you pause music or other (trigger based on `media_player` state `playing`; does not differentiate types of play).


```yaml
service: script.notify
data:
  title: "Home Assistant"
  no_greeting: false
  ignore_sleep_mode: false
  ignore_presence: false
  do_not_queue: false
  send_to_phone: false
  bypass_sound_fix: false
  speak_targets: []
  speak_on_voice_boxes_with_presence: true
  speak_on_last_voice_box_triggered: false
  message: |-
    {{ ["Your phone is done charging.",
        "You can unplug your phone.",
        "Your phone's battery is now full.",
      ] | random }}
```

See `Goodies` folder for other examples.

### Requirements:

- Toggle `input_boolean.sleep_mode`
  - Can do the same with shower mode, guest mode, whatever you want...
  - No TTS while sleeping (activated/deactivated manually/by voice), queue notification instead

- Group `binary_sensor.any_presence_inside_the_house`
  - No TTS while no one is detected inside the house (motion/presence/etc), queue notification instead

- `speak_on_voice_boxes_with_presence`
  - Needs some kind of presence detection (motion, etc) for each media player

- Group `media_player.all_media_players`

- `todo.queued_notifications`
  - Store notifications for later "read" in this list (see below)

- `send_to_phone`
  - Requires Home Assistant companion app
  - WARNING: The service `notify.mobile_app_xyz_s_phone` must be changed according to your phone's name as registered by Home Assistant
  - You can swap this with `notify.persistent_notification` if you prefer (or add it as an extra option)

- An audio file for speaker issue (see below)

### Queueing for presence

A combination of motion/presence/modes & script-level overrides can be used to queue notifications thanks to the new [TODO integration](https://www.home-assistant.io/integrations/todo). With a simple automation, you can then read whatever you missed with TTS, whenever/however you decide.

### The "sleeping speaker" issue

The 3.5mm jack from the Intel NUC hosting Home Assistant is not great. Home Assistant seems to poorly handle wake/sleep states for audio which strips/distorts the beginning of TTS play (far too often). A simple bell-ring type audio file can be played immediately before TTS to wake up the audio (check the mp3 file in this folder, it works well-enough). In most cases, it gets cut-up and overridden so fast it is barely noticeable. There are other solutions for this issue, but they usually require an external component; this is simpler until the problem gets fixed.

```yaml
- service: media_player.play_media
  data:
    media_content_id: media-source://media_source/local/bell-ring-1.mp3
    media_content_type: audio/mpeg
  target:
    entity_id: media_player.vlc_telnet
- delay:
    hours: 0
    minutes: 0
    seconds: 0
    milliseconds: 100
```

PS: If after a restart, you notice the Intel NUC's jack is no longer outputting any sound, try rebooting Home Assistant. It seems that sometimes the OS is not properly recognizing the sound card on boot and only registers a `Dummy output`....

# scripts/esphome_notify.yaml

An all-in-one notification script, that does its best to solve current issues.

Long story short, instead of outputting TTS directly (i.e. letting `voice_assistant` do its job), pass the text to a Home Assistant script in `on_tts_start`. Don't use `speaker` or `media_player` property inside `voice_assistant`, let the script do the fixing and the playing.

```yaml
on_tts_start:
  - homeassistant.service:
      service: script.esphome_notify
      data:
        ww_switch: switch.voice_box_wake_word_enabled
        target: media_player.voice_box_player
      data_template:
        message: "{{ msg }}"
      variables:
        msg: return x;
```

In some cases, the same thing can be accomplished directly inside the ESPHome configuration (via automation), but doing it externally allows for a single-script approach to "fix all"; consistency.

You will want to add the "sleeping speaker" fix from `notify.yaml` right before `tts.speak` call, if you are encountering the issue here also.

### The long story

NOTE: This might not apply to all boards. This fix requires `framework: arduino`, as `media_player` is not currently supported with `esp-idf`...

When using `speaker` or `media_player` in `voice_assistant`, in conjunction with wake word detection, the S3 has a hard time copping with the load. The audio comes out entirely distorted (you can almost feel the poor little boards pushing themselves to the limit...). I would care to guess the constant reboot of the pipeline every 5 seconds doesn't help.

You will want to disable the voice pipeline while TTS is playing. This is what the `ww_switch` property does. Deactivate wake word on the S3 to turn off voice pipeline, wait for a few ms, play the actual TTS, then reactivate the wake word once the S3's `media_player` returns to `idle`.


# BONUS: scripts/voice_get_greeted_message.yaml

AKA The English Butler (or whatever you want it to be).

A script used by the notification scripts above, that uses previous trigger and time of day to greet you properly. You can change the messages used to customize the output with every call. Hopefully this can be improved with voice identification soon.

In the above scripts, `fallback_prepend` is used in `esphome_notify` since this is meant to be used in the `STT>TTS` scenario. For `notify`, the append version with the question mark feels more natural as it will be used to alert you. For example:

STT>TTS:
```
> user: Is the front door closed?
> ha: Yes, the front door is closed, sir.
```

automation notify (from whatever trigger):
```
> ha: Sir? You forgot to close the front door.
```

# ESP32 Voice Boxes

## esphome/voice_box.yaml

An example configuration for ESPHome `voice_assistant`.
Used with an S3, but should work with others, hardware-permitting.

- i2s_audio
- dual microphones (INMP441 connected as per the [docs p11](https://invensense.tdk.com/wp-content/uploads/2015/02/INMP441.pdf))
- internal `media_player` (through PCM5201a with 3.5mm jack output).

## esphome/voice_box_external.yaml

An example configuration for ESPHome `voice_assistant` that plays TTS on an external media player through script.
Used with an S3, but should work with others, hardware-permitting.

- i2s_audio
- dual microphones (INMP441 connected as per the [docs p11](https://invensense.tdk.com/wp-content/uploads/2015/02/INMP441.pdf))
- play audio to external `media_player` through Home Assistant script.
