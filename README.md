# Smart Home Enhanced

Below, you will find a detailed playback of my trials and errors in the Smart Home world. Hopefully, this can give you some pointers, and you can avoid making the same mistakes I did. Just my 2 cents, unfiltered.

> WARNING: This includes past facts that are no longer relevant/correct (hence the changes over time), be sure to read the entire thing to get the full picture.

You will find examples for Home Assistant-related configuration in the subfolders.
Most of it is meant to work together, and might need some tweaking if you want to use only part of it. Each folder has a list of the requirements for each part.



## An Expected Journey

### The "Initial" Quest [Fall 2021]

- Automate common tasks
  - Lights based on motion & light (time of day)
  - Heat (electric) on/off based on opening/presence
  - Alarm based on motion/opening/presence
  - Fans based on climate/time
- Climate checking
  - House, Outside, Greenhouse
- Single hub
- Devices with direct-to-hub connection (no 3rd party app, addon, etc)
- Reliable (enough)
- No WiFi crowding
- Decent range support
- Retain basic control in the event of an Internet outage (i.e. offline control)
- New devices supported rapidly (enough)
- Save power wherever possible
  - With devices themselves (no power hungry protocol!)
  - And with what devices bring to the table
- Limit cost for the whole system; devices and sensors add up quickly, even cheap ones.

#### Implementation

- Zigbee protocol for all devices whenever possible
  - Avoids WiFi crowding
  - Easy support for new devices thanks to a defined "communication protocol" (albeit not always followed properly by manufacturers)
  - Retain control even if WiFi router fries
  - Decent range & stability thanks to mesh networking
- Amazon Echo 4 for hub + Echo Flex for "secondary room" control
  - Zigbee support
  - Limited local control of Zigbee device in the event of an Internet outage.
  - Decent list of supported devices
- Motion sensors
- Temperature & humidity sensors
- Presence modes set via voice or app automation, coupled with motion sensors
- Transformation during heavy renovations; opt for "direct" devices (as in smart switches, not smart bulbs, and preferably the "in wall boxes/electrical panel" kind)
  - Note: Comes out much cheaper. The same can be done without renovating in most cases (ZBMini to the rescue!), but my scenario it made it a no brainer

#### Results

On average, I decreased the whole-house electrical consumption by 20% (data limited to the months prior to renovations making a difference also - insulation is your friend!). Most of it, of course, came from taking control of the "dumb" electric heaters, even if just on/off (presence linked). You can pay back the entire system in a single winter, and then some, with those savings.

Automating lights, on the other hand, made a difference on practically; this is for you that always come home with your hands full... you don't need the other hand!

## The Desolation of Consumer Systems

Smart Home consumer systems (the big three) have come a long way since their inception, automation, machine learning, voice.... And yet, they remain essentially clueless about many of their users' needs; or so I found out.

### Automation

Perhaps THE single most baffling issue, automation itself. There are many ways to automate a home, many ways to improve upon it over time (and errors). But the most basic of needs, that is, being able to act upon a certain set of conditions, is far too limited in many cases, not to say nigh impossible.

When you begin your journey, you start with the basics, `time <> light`, `motion <> light`. As time passes on, you add to the smart home, and quickly realize, there are better ways to do some things. And then, you quickly realize how limited your system is in terms of automation; `if then`, but... where is `else then`, `else if then`, where is `if and then`, `if or then`? You start creating automations for the sole purpose of activating/deactivating other automations, because that's the only way to do what you want. And eventually, you find yourself in front of a fantastic automation idea that simply cannot be done (in any reasonable fashion). But, you know, it's a damn great idea, so what do you do? Link another automation platform to your setup? Find another consumer system that can handle it? Either way, it's going to get a lot more expensive than what the automation would ever bring. And you get no guarantee said new system will cover future similar needs.

The core problem: the software of your "base" system is crap.

### Don't cut the line!

> - User: {XYZ}, what's the time?
> - {XYZ}: Sorry, I'm having trouble connecting to the Internet.

When I got the Echo 4, it had offline control of basic functionalities, that is controlling Zigbee lights and such. It was the first thing I tried after configuring the thing. I unplugged the Internet, and then the router, in turn, to confirm the limits on that front, with the intent of returning it right away should it not perform as expected; but it did. And then a few months later, Amazon decided it was fine to just remove it all; without so much as a warning.

It might not happen a lot (anymore), but sometimes, the Internet goes down, and sometimes your router gets fried; same goes for power, assuming some of your devices are on UPS backups and should remain functioning. Guess how I figured out Amazon had removed the support for the feature on the Echo 4? _Which, by the way, was never confirmed as "removed" by Amazon's support, only "not supported" with a nice link to the device's page that mentioned absolutely nothing on this...._

When an outage happens, and your entire house becomes essentially "broken", you quickly realize how much help it really is. Of course, you can revert to using buttons, and switches. But hey, it's not like it's hard to support offline control... since it was.

### New Features / Updates

This one was perhaps the proverbial nail.
Monthly update notes on Amazon's side were completely abandoned (and have been since the end of 2022). These days, it seems UI updates are pretty much the only thing happening.

Entering a new segment, I always keep track of bugs, and improvements that could be made to a system/platform. I did that for many months with Alexa and the couple of Echo devices I have. Eventually, I saw my most basic (understand 'no brainer') suggestions go entirely ignored by the support, and just gave up. As for bugs, some got corrected (eventually), and others kept reappearing once every few updates. Quality control... what's that?

### The "Soon-After" Quest [Fall 2022]

- Time to replace the Echo 4 as a hub.
  - It is obviously no longer meeting the standards I set for it; on more than one front...
  - Would be nice to retain voice somehow (TTS more than STT)
  - Definitely retain Zigbee as protocol
  - Need far more control now that I've "gotten a taste" for it (oh boy...)

#### Implementation

- [Home Assistant](https://www.home-assistant.io/)
  - Large automation handling system (if and/or then else if then else then, and then some!)
  - Customizable dashboards (yes, not everyone wants/needs the same "main" display on a Smart Home... here, you build it for your needs)
  - Loads of integrations (devices, services supported)
  - Enhanced presence detection with phone app & router support
  - Statistics for your sensors (more than you could ever use)
  - ESPHome! (If you tinker... Perfect for a greenhouse with multiple sensors all in one board!)
  - Speaking of tinkering, there's a lot more in there for you if you don't mind a bit of YAML/jinja for whatever remains (still) in plain-text configuration (less and less needed with every update; more stuff is brought to the UI regularly)!
  - Installed on an Intel NUC (older gen)
    - Much better performance than a RPI, traded for a bit more power usage
  - Could even see decent voice support soon!
  - Open Source!
- [Zigbee2MQTT](https://www.zigbee2mqtt.io/)
  - Sonoff ZBDongle-E (EFR32MG21 version) for the Zigbee adapter (get 2 and flash one as router if you need more range)
  - Thousands of devices supported

#### Results

The difference can hardly be quantified. You can automate however you want, with ease. You can customize however you want, with ease. Statistics help you "better" your smart home, and your home in general...

I even brought a few devices "back to life" (that never worked with the Echo 4 hub).

The Sonoff dongle is pretty stable, works well with Home Assistant & Zigbee2MQTT.

The whole thing lacks a proper voice (google translate...), but you can easily enough link Alexa to the system and use a Home Assistant script for tailored notifications (TTS). For now, that's fine.



## The Battle of "Everything Local"

With quality control in free-fall (or so it seems), the "local" aspect is more than ever appealing; be it for the hacking potential of buggy updates, or the "what the heck is my data doing on a server in {insert_country_across_the_globe}" where "access control" is a sticker on a door. You add "greed is good" on top, and you have most companies giving up on proper support; and maintaining products for a decent amount of time has become somewhat of a fairytale... better to release a new device for sale every year, and call that "an update".

At this point, most of my system is now local. I can control the house (and greenhouse) through automations/scripts without even pinging the WiFi router. Stuff like weather can still be queried online, but you can cherry-pick providers; about phone notification via app, see [docs](https://companion.home-assistant.io/docs/notifications/notification-details). Remains voice; it's never been a really high priority since most of the system runs itself without interaction. It took some time to get there and have satisfying results, but satisfying it truly is. Alexa has become nothing more than a voice-enabled timer/clock on the control side (Speech-to-Text), and a way to get voice notification from automations (Text-to-Speech).

And then, Home Assistant goes full bore on the voice development! The idea of removing Alexa entirely takes a firm place in the back of my mind. I let some time pass, and eventually, I just have to try.

### The "Eventually" Quest [2023]

- Mo devices, mo... less power!
- Local Voice

#### Implementation

- Solar panels addition
  - Goal to cancel the house's constant consumption (whatever the house consumes when nothing "significant" is running)
  - Note: You will want to check your house's "passive" consumption to figure out sizing here; check solar forums
  - Limited to day-only, since no battery (gain not sufficient to warrant the addition, unfortunately)
- Power meter sensors for solar panels and whole house
  - This proved a bit challenging at first with the pair of clamps I got. I had to do the import/export logic in software since they happened to be one-way models. Still, I ended up within about 5 kWh from my actual monthly billed amounts. Pretty good for 20$ clamps.
- Water heater control
  - Simple on/off based on best time for solar production
- Switch to finer heat control
  - Use "generic thermostat" platform of Home Assistant to drive "dumb" heaters with external sensors
- Text-to-Speech with [Piper](https://www.home-assistant.io/integrations/piper/)
- Speech-to-Text with [Whisper](https://www.home-assistant.io/integrations/whisper/)
- Wake word with [porcupine](https://github.com/rhasspy/hassio-addons/tree/master/porcupine1)
  - Note: [openWakeWord](https://github.com/home-assistant/addons/blob/master/openwakeword) seems decent too, it just lacks "interesting" wake words at the moment
- A few ESP32 S3 boards with proper components to replace Echo devices wherever voice is "desired" (see `VoiceAssist` folder)

#### Results

Another 15-20% reduction in grid import! Most of that would be the solar panels covering "the base" of the house during the day. But I also tailored many automations to spread power usage throughout the "best hours" of solar production, water heater, ventilation, etc.

On a side note, and before I dive into the voice aspect, I tried the "Silicon Labs Multiprotocol" addon. I wanted to upgrade the firmware of my Sonoff ZBDongle-E adapter after noticing a few connectivity issues (ZBMinis going unavailable for a while in turn) that I suspected were hardware limits (a few too many ZBMinis on the network; they might be really nice, but they sure have crappy antennas, probably not helping!).
After some searching, I found [darkxst's tool](https://darkxst.github.io/silabs-firmware-builder/), which led me to try the silabs addon, thinking it might solve the "hardware limits" in question, by removing them from the equation altogether (Zigbee on Home Assistant's side). I had no success there. It worked (ran, paired, reported, everything...), but had far too many issues (mainly commands not reaching devices; maybe it works better with ZHA thant Z2M...). I reverted to a Zigbee-only firmware without issue. The first upgrade (after flashing RCP firmware) forced me to re-pair all my devices with Zigbee2MQTT (a bit of a shore with over 30 devices currently), but the second (after flashing to NCP 4.3.1), kept all my devices (yay!). The new NCP firmware did not solve the original problem either. After some investigation (on-again-off-again problems are always troublesome), I would have to say it has nothing to do with hardware, and instead is caused by something going "wrong" after Home Assistant updates, likely in relation to Zigbee2MQTT. But here is the catch, some updates cause the problem, and some fix it; it comes and goes as it pleases, nasty little bugger. I actually noticed it earlier in the year, but then it disappeared for months, so I gave up; when it reappeared in the fall, is when I decided to upgrade the firmware. PS: It's gone again as of 2023-12. _Long story short, if you have that specific "ZBMinis randomly going unavailable for a while" issue, just know that a firmware upgrade, or offloading the Zigbee process will not help._

Onto the voice!

First, TTS, because that's easy. It worked right away (albeit with a few kinks at the moment, see `VoiceAssist` folder for workarounds), I just plugged a speaker in the Intel NUC (with the VLC addon), and [Piper](https://www.home-assistant.io/integrations/piper/) did the rest. I changed my notification script to use the Piper service, and took the time to refresh it a little (always a good practice after a few observations start floating in your head on how to improve this or that... before they float too far).

Second, STT. Now that's another beast altogether. Between wake word detection, and proper speech recognition, you have two massive issues looming. As I said, I didn't use it much anymore, timers, reminders, the occasional "manual" switching, so I didn't see much of an issue to try the new [Whisper addon for Home Assistant](https://www.home-assistant.io/integrations/whisper/), even if I expected it not to be perfect just yet. First step: mute Alexa!

I have to say, I was surprised by the quality of the STT, for something local, and recent, it's already pretty damn good (much better than the early days of dictation software, that's for sure!). I'm currently BETA testing on ESP32 S3, armed with a couple of INMP441 microphones, and a PCM5201a with 3.5mm jack output. There are a couple of quirks, mostly hardware limitations/software optimizations (see `VoiceAssist` folder). Noise affects the quality of the STT quite a bit; larger models help, but at the cost of performance (from <1sec to 2-4sec). Be sure to tailor the settings based on what your hardware can handle and what you determine as "acceptable". No doubt updates will smooth out these issues one by one (or all at once! one can dream...).

Quick note about wake word, it's pretty good; a few false positives from playing media at times. As mentioned somewhere else, wake word seems much less affected by surrounding noise, which is definitely a plus, hopefully STT will follow on that path.



## The Sequel

And now, my 2 cents!

The big 3 are left behind, by a large margin, in terms of "smart home" capabilities. Their devices might be nice (the Echo 4 is definitely a good speaker), they might have superior voice technology (at the moment), but that's where the benefits end. And I don't believe the big push towards "AI" they are all enamored with is going to change much on that front. Better voice? Sure. Better home control? Doubt it. If you want a proper automated home, look elsewhere.

### Tips

- Do set yourself some initial goals the smart home is supposed to accomplish, some example...
  - Make switches a thing of the past (and outages)!
  - Integrate alarm system (i.e. get enough sensors to cover the required areas)
  - Cut down house power consumption (careful about over-estimating things on that front)
  - Max x$ investment (careful about under-estimating here...)

- Do get a protocol other than WiFi for "most" devices from the very beginning
  - If not, before you know it, you might reach the limit of your router
  - Cheaper in the long run, protocols dedicated for IoT are much less power-hungry (be it battery/plugged)
  - Zigbee was my choice, and so far I'm very happy with it
    - I can reach about 10-15 meters outside the house (very thick walls) depending on location, without killing batteries in no time; enough for my needs. NOTE: The mesh network could extend from there and increase the range, with a supporting plugged device in the location, or an adapter in [router mode](https://github.com/itead/Sonoff_Zigbee_Dongle_Firmware/tree/master/Dongle-E/Router).

- Do pick a brand or two, well-supported by the system you will use, and stick with them; better consistency across sensors.

- Don't buy two dozen sensors/devices at once at the beginning
  - It will take you some time to acclimate to the system of your choice, and get a good control over it
  - If you find the devices you got are not up to par, you can try others without breaking the bank

- Do check out presence/occupancy sensors, although less popular for now (growing fast), they might be better than PIR motion sensors for some applications (stationary person detection, at the cost of smarter positioning required to avoid false-detections with things like robots, curtains...).

- Do get a system that offers decent statistics on your smart home, it will help you figure out a lot of things
  - Where savings can be made (power meters are your friends)
  - Where problems might arise in the house (and "fully-established" ones...)
    - Goes double if you DIY a lot, and aren't afraid of repairs and such
  - It might just surprise you to learn some things!

- Solar panels are a great addition to a smart home (properly sized), you can cancel-out the power needs of your new devices during the day with just one or two usually (depending on what extras you also want to cancel-out), requiring a small(er) investment.
  - Forget about a battery system if you are looking for returns only. In the "cancel-out" scenario, you are looking at perhaps 1% extra at year 10, but then you will likely have to replace it; at year 20, you'd be at -1%, assuming batteries don't increase in price until then... (PS: these are calculations I made for my own case and might not apply in yours.)

- [2023-12] Don't switch to Home Assistant's voice pipeline just yet if you have one of the big 3, unless you're fine with a few quirks and a somewhat lesser overall quality
  - There is also a need for better integration within Home Assistant's subsystems: queuing of TTS, automatic volume/play/pause handling, and things like that, have to be done through "hacks" or clever templating for now, not ideal if you're just coming into the smart home world.
  - If you only care about TTS, then it's fine, ignore this point. There are some minor quirks like what I mentioned above, but Piper will do the job just fine, at no extra cost. It has a massive list of voices, you will have to figure out the one(s) you prefer, in both quality (some are pretty crappy) and tone.

### Home Assistant

#### The Pros

- Read the above

#### The Wants (AKA Cons with hopes of update)

- Get rid of the auto-formatting in templates EVERYWHERE! It's like the darn thing is trying its best to wrap lines where it really shouldn't! Every time you want to update one, you have to put it back together first to be able to read it... and as soon as you save, it's back to non-sense formatting...

- Less nesting of menus (yes, there is a lot of stuff in there, so automatically, lots of menus are required, but some "primary" things are just painful to reach without creating custom links in dashboards)
  - How about a better `Settings` page with direct access to sub-menus for larger screen sizes? Mine's about 40% empty with that little rectangle in the middle, with the browser not even in full screen...

- Better sound support with Home Assistant OS; probably NUC-specific, but I've seen loads of posts on audio issues while searching for mine. I'd really like to stop double-restarts whenever the sound card isn't detected on the first one! Plus it would be nice if it just worked without the VLC telnet workaround...

- Less "thought for RPI" design
  - Here is the main reason behind this one: I'm no expert on OS dev, but it sure seems like the Intel NUC (older gen - soon to be 10Y old) could do much more with voice at the moment; especially when it comes to handling larger models better/faster by better using the available resources.
  - With smart homes getting more and more powerful, the time will eventually come that RPIs just won't cut it anymore (or they will have to become much more powerful, either way...); more powerful machines might suffer because of a lack of optimization for "more powerful"; this is especially true now that voice has passed the corner, and presumably prediction systems to improve STT for individual users are to follow. Of course, I could be wrong; smart home systems could get optimized to the point of running on a GameBoy...
  - Obviously, that doesn't mean "make it a power plant", just equal optimization for different hardware levels.

##### Voice specific

- Integration of "greeting" (similar to `VoiceAssist/scripts/voice_get_greeted_message.yaml`) directly in Assist's configuration.
  - With support for:
    - Time of day vs previous time triggered (to add extras like "Morning", "Afternoon", "Evening")
    - Some kind of "toggle" to allow append/prepend depending on context and previous point
    - Voice identification

- Intents / IntentScript in UI, but I expect this is just around the corner, since it isn't much different from scripts/automations
  - A special kind of template for TTS output allowing similar processing as `sentences`, but in reverse, would be nice to add some easy randomness without using `[string1, string2, string3] | random` everywhere
    - > `(Morning|Good Morning). [How are you?] (It's|Temperature is) {value}{unit} outside.`
  - Maybe a template field in entity Voice Assist settings allowing to customize `speech` output per-entity? Same for `sentences`.
  - Better TTS support for units of measurement
    - Currently, will only spell letters; "twelve W" for "12W", or "fifteen point two degrees C" for "15.2°C", "km/h" is probably the worst...

- An "Intent/IntentScript" repository (a bit like add-ons) would be nice
  - Allow selection of whatever intents a user want their Assist to support
  - Allow creation of helpers automatically on import based on what is required (or linking to existing ones)
  - Could even allow creation of "default" automation/script on import

### Devices

As for devices, I tried a few brands (Zigbee-only):

- `Moes` is fine
- `Sonoff` is pretty good (my primary choice)
  - SNZB-02 humidity needs calibration (off by quite a lot, 5-10% usually)
  - ZBMini has a crappy antenna, as in, battery-sensors will have a better link quality at 3 times the distance, but as long as they are close enough to still connect, it doesn't matter too much (obviously a bit more power used, but they're wired...)
- `Zemismart` has connectivity and state freeze issues
- `Linkind` is battery-hungry like you wouldn't believe (might as well buy a battery factory)...
- `OSRAM/Ledvance` is pretty good too (except for the below), though not in the same price range
  - That damn re-pairing procedure; on 5s, off 5s, 5 times, seriously, who thought that was a good design choice?
- `MatSeePlus` is fine, a bit stubborn at times to connect

