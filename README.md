> Author is actively editing this guide. If you find error, plz report it.

**Last Update:** [date=2025-07-05 timezone="Asia/Karachi"]

This guide assumes that you already have.

* A running instance of Home Assistant and accessible at: `http://homeassistant.local:8123`
* Any google speaker (Google Home Mini/Nest)

Follow the following steps **in same order**.

1. Go to ⚙️ `Settings` tab in left side menu.

2. Click on 🎛️ `Devices & Services`.

3. Click on ➕ `ADD INTEGRATION`.

4. Search for `Islamic Prayer Times` and install it. Configure it and provide your coordinates (latitude and longitude) correctly. You will see an integration card like this:
[wrap="center"]
![image|388x163, 75%](upload://cpDJNUJThlcT5iPpRUKVvafbnWT.png)
[/wrap]

5. Click on this `integration card`, and then configure it.
[wrap="center"]
![image|690x289, 75%](upload://9ys4Q8VTdqQEvM1bVM9uDa7N4dj.png)
[/wrap]

6. Choose options as you please and then click `SUBMIT` button.
[wrap="center"]
![image|436x500, 75%](upload://d9toHpcJp4JxuXaDiZ1FoKQSmJ2.png)
[/wrap]

7. Then using 🔧 **file editor** edit your **configuration.yaml** file and paste the following code at the end and save 💾 **configuration.yaml** file.

```
sensor:
  - platform: template
    sensors:
      salah_fajr:
        friendly_name: "Fajr (with offset)"
        value_template: "{{ (as_timestamp(states('sensor.islamic_prayer_times_fajr_prayer')) + 1140) | timestamp_custom('%Y-%m-%dT%H:%M:%S+00:00', false) }}"
        device_class: timestamp
      salah_dhuhr:
        friendly_name: "Dhuhr (with offset)"
        value_template: "{{ (as_timestamp(states('sensor.islamic_prayer_times_dhuhr_prayer')) + 3840) | timestamp_custom('%Y-%m-%dT%H:%M:%S+00:00', false) }}"
        device_class: timestamp
      salah_asr:
        friendly_name: "Asr (with offset)"
        value_template: "{{ (as_timestamp(states('sensor.islamic_prayer_times_asr_prayer')) + 60) | timestamp_custom('%Y-%m-%dT%H:%M:%S+00:00', false) }}"
        device_class: timestamp
      salah_maghrib:
        friendly_name: "Maghrib (with offset)"
        value_template: "{{ (as_timestamp(states('sensor.islamic_prayer_times_maghrib_prayer')) + 1) | timestamp_custom('%Y-%m-%dT%H:%M:%S+00:00', false) }}"
        device_class: timestamp
      salah_isha:
        friendly_name: "Isha (with offset)"
        value_template: "{{ (as_timestamp(states('sensor.islamic_prayer_times_isha_prayer')) + 300) | timestamp_custom('%Y-%m-%dT%H:%M:%S+00:00', false) }}"
        device_class: timestamp
```

8. Then edit your **automation.yaml** file and paste the following code, and then save your 💾 **automation.yaml** file.

```
- id: '1726512717238'
  alias: Four Azans Automation
  description: ''
  trigger:
  - platform: time
    at: sensor.salah_dhuhr
  - platform: time
    at: sensor.salah_asr
  - platform: time
    at: sensor.salah_maghrib
  - platform: time
    at: sensor.salah_isha
  condition: []
  action:
  - action: media_player.play_media
    target:
      entity_id: media_player.google_home_mini
    data:
      media_content_id: https://www.islamcan.com/audio/adhan/azan1.mp3
      media_content_type: audio/mpeg
    metadata:
      title: Adhan.mp3
      thumbnail:
      media_class: music
      children_media_class:
      navigateIds:
      - {}
      - media_content_type: app
        media_content_id: media-source://media_source
  mode: single
- id: '1726513324106'
  alias: Fajr Azan Automation
  description: ''
  trigger:
  - platform: time
    at: sensor.salah_fajr
  condition: []
  action:
  - action: media_player.play_media
    target:
      entity_id: media_player.google_home_mini
    data:
      media_content_id: https://download.tvquran.com/download/TvQuran.com__Athan/TvQuran.com__01.athan.mp3
      media_content_type: audio/mpeg
    metadata:
      title: FajrAdhan.mp3
      thumbnail:
      media_class: music
      children_media_class:
      navigateIds:
      - {}
      - media_content_type: app
        media_content_id: media-source://media_source
  mode: single
```

9. 🔁 `Restart Home Assistant`.

10. Then go to your `DASHBOARD` and `EDIT DASHBOARD`.

11.  Click `Add Card`, and choose an `Entities` Card.
[wrap="center"]
![image|186x84, 50%](upload://hNjpRjS6zO5uZdIgKfy7x1qIwV8.png)
![image|391x152, 75%](upload://mSO1DsWGm70lVct5ETyFH176piD.png)
[/wrap]
<br>

12. Then configure `Entities` Card and click on `SHOW CODE EDITOR`.
[wrap="center"]
![image|690x455](upload://uuITLqv8LyK6mA5xiVZTShCzNBI.png)
[/wrap]

13. Remove all the entries from the card.
[wrap="center"]
![image|627x438, 75%](upload://dvnAWlHp1uFLlhZw6gI3I6EYlqc.png)
[/wrap]

14. Paste the following code here.

```
type: entities
entities:
  - entity: sensor.salah_fajr
    format: time
    name: Fajr
  - entity: sensor.salah_dhuhr
    format: time
    name: Dhuhr
  - entity: sensor.salah_asr
    format: time
    name: Asr
  - entity: sensor.salah_maghrib
    format: time
    name: Maghrib
  - entity: sensor.salah_isha
    format: time
    name: Isha
title: Prayer Times
```
<br>
15. Done. انشاءاللہ you will hear Adhan at its specified time.

# Caution:
In `automation.yaml` file, do not forget to change **entity_id**, matching with your Google Home Mini/Nest/Speaker ID.

Last Updated: [date=2024-10-22 time=22:24:00 timezone="Asia/Karachi"]
