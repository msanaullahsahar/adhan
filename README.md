# Adhan Automation. Prayer calls using Home Assistant and Google Home Mini

This guide is written especially for newbies who have just a google-home-mini and are trying to make it call for adhan. A lot of people have already done this, however even reading all posts on the internet related to adhan automation, I honestly admit that I remained unable to follow instruction and couldn't get any success. I watched a lot of tutorials on Home Assistant and made myself familiarize with HassIO platform. By doing so I got to a point where I can install HassIO platform on my Raspberry Pi Zero W and automate adhan using my google home mini.

> To follow this tutorial, you must have;

1. Raspberry Pi (Zero/2/3/4)
2. SD card (8/16 GB)
3. Access to home router
4. One google speaker (I am using google home mini)

Please follow the steps **in order**.

1. Download Home Assistant Platform **HassOS image** from [official website](https://www.home-assistant.io/hassio/installation/). Download the correct image against your Raspberry Pi model. Since i am using Raspberry Pi Zero W, I have downloaded **hassos_rpi0-w-2.12.img.gz** image. The downloaded image has an extension **.gz** which means it is a highly compressed archive. Right-click on it and extract **.img** from it. To extract it I'd recommend 7zip or WinRar. The end result is you will have an image with **.img** extension. In my case, I have **hassos_rpi0-w-2.12.img**
2. Download [**blena etcher**](https://www.balena.io/etcher/), a program to burn HassIO image on SD card, and install it.
3. Put your SD card in an SD card reader and connect it to your laptop/PC.
4. Once the PC/laptop detected your SD card, open **blenaEtcher**, click on **select image**, and choose your image that you have just extracted. Next click on **change** under the **drive icon** and select your SD card and click **continue**. Next click **Flash** and wait for the process to get completed. Once you get notification **Flast Complete**, close the **blenaEtcher** and look for a drive in your PC with name **hassos-boot**. At this point, windows will ask you to format SD card. Just cancel/ignore this request. Open **hassos-boot** drive and make a new folder in it with name **CONFIG**. Open this folder and create another folder in it with name **network**. Again open this folder and create a file in it with name **my-network**. Open this file with notepad and paste the following code in it. After this, save and close the file. This information will help your raspberry-Pi to connect to internet via WiFi. Don't forget to replace **SSID** and **password** in the code below with correct values of your WiFi.

**WiFi Settings File**

```
[connection]
id=my-network
uuid=72111c67-4a5d-4d5c-925e-f8ee26efb3c3
type=802-11-wireless

[802-11-wireless]
mode=infrastructure
ssid=NameOfYourWiFi


[802-11-wireless-security]
auth-alg=open
key-mgmt=wpa-psk
psk=YourPassword

[ipv4]
method=auto

[ipv6]
addr-gen-mode=stable-privacy
method=auto
```

**Now set up your Google Home Speaker (google home mini) before proceeding if your google home mini is not already connected to your network.**

5. Remove the SD card from PC, put it in Raspberry-Pi and power-on you raspberry-pi. Wait for a couple of minutes (in my case I waited for 10 minutes) and then visit your router homepage. For example, my router homepage can be accessible at **192.168.0.1**. The address of your router may be different. Since every router has a different setup, unfortunately, I can not provide much detail on this. Find the IP address of your Raspberry-Pi on your router homepage and note it somewhere. Then create a port forwarding rule and associate port **8123** with the IP address of your raspberry-Pi.

**VIDEO INSTRUCTION FOR THE STEPS BELOW IS ALSO AVAILABLE**

> [YOUTUBE VIDEO](https://youtu.be/wFA8qsu4wPE)

6. Now open an internet browser and type the IP address of your raspberry-Pi along with **:8123**. In my case, it becomes something like **http://192.168.0.25:8123**. At this point, you will be presented with home assistant homepage with information that it will be ready in 20 minutes. In my case, it took more than 30 minutes to get ready. This is because raspberry-Pi zero w has very less RAM about 512 MB. The more the RAM the faster would be the process.
7. The homepage refreshes itself and it will ask you about your name/username/password. Supply this information and go ahead. In the next step, it will detect your time zone. Just go head as we will set this in **configuration.yaml** file. At this point, if your Google home mini is already connected to the internet and working independently, it will be detected automatically. See my [video](https://youtu.be/wFA8qsu4wPE).
8. On home assistant main page, look on the right-hand sidebar and click on **Hass.io**. Then click on **ADD-ON-STORE**. Under official add-ons, look for **configurator**. Click on it and then install it. After installation, you need to start it by pressing **start** on the same page. Once it starts **open web UI** will appear. Click on it and you will be presented with an editor where we can modify configuration files.
9. On this page, look on the top for a **folder** icon in the blue bar. Click on it and a list of files will appear. Click on **configuration.yaml**. Now copy the code below and paste it in the editor on the right-hand side.

> **WARNING**

Don't forget to change latitude, longitude, elevation and time zone to match your location. To find these values, go to this [website](https://www.maps.ie/coordinates.html) and enter your address. Copy your values and repace the corresponding values from the code below. If you don't know your time zone you can check it on this [website](http://www.timezoneconverter.com/cgi-bin/findzone.tzc)

> Setting **configuration.yaml** file.

```
homeassistant:
  latitude: -28.003696
  longitude: 153.4236469
  elevation: -1
  unit_system: metric
  time_zone: Australia/Brisbane
  
default_config:
discovery:
media_extractor:
sun:

sensor:
  - platform: time_date
    display_options:
      - 'time'
      - 'date'
      - 'date_time'
      - 'date_time_iso'
      - 'time_date'
      - 'time_utc'
      - 'beat'
  - platform: islamic_prayer_times
    calculation_method: mwl
    sensors:
      - fajr
      - sunrise
      - dhuhr
      - asr
      - maghrib
      - isha
      - midnight

# Text to speech
tts:
  - platform: google_translate

group: !include groups.yaml
automation: !include automations.yaml
script: !include scripts.yaml
```

10. Save the file by clicking on the **floppy drive icon** on the top blue bar and then close it by clicking **X** icon.
11. Again click on **folder icon** and then click on **automation.yaml** file. Copy and paste the code below in the editor on right-hand side.
Caution: In the code below, against **entity_id**, don't forget to replace **sahar_google_mini** with your Google Home Mini ID.

> If you don't remember your google home mini name, just open **Google Home app** on your android phone *(sorry guys I have only android phone and don't know how to do this on ios)* and on the bottom bar, tap on **home icon**. You will see the device name under your device icon.

> In the code below you have to enter your google home mini name in small letters. If the name is more than a one-word name, you have to put **underscore** after every word. Let say the name of your device is **My Home**, it should be written as **my_home**. If it is one-word name only, just type it in small letters.

> Setting **automation.yaml** file

You need to replace values at **TWO** such places in code below.

```
# Automation for Dhuhr, Asr, Maghrib, and Isha Adhan
- action:
  - alias: ''
    data:
      entity_id: media_player.sahar_google_mini
      media_content_id: https://www.islamcan.com/audio/adhan/azan1.mp3
      media_content_type: audio/mp3
    service: media_extractor.play_media
  - data:
      entity_id: media_player.sahar_google_mini
      volume_level: '0.8'
    service: media_player.volume_set
  alias: Adhan
  condition: []
  id: '1517693010922'
  trigger:
    - platform: template
      value_template: '{{ as_timestamp(strptime(states("sensor.time_date"), "%H:%M, %Y-%m-%d")) == as_timestamp(strptime(states("sensor.islamic_prayer_time_fajr"), "%Y-%m-%dT%H:%M:%S")) }}'
    - platform: template
      value_template: '{{ as_timestamp(strptime(states("sensor.time_date"), "%H:%M, %Y-%m-%d")) == as_timestamp(strptime(states("sensor.islamic_prayer_time_dhuhr"), "%Y-%m-%dT%H:%M:%S")) }}'
    - platform: template
      value_template: '{{ as_timestamp(strptime(states("sensor.time_date"), "%H:%M, %Y-%m-%d")) == as_timestamp(strptime(states("sensor.islamic_prayer_time_asr"), "%Y-%m-%dT%H:%M:%S")) }}'
    - platform: template
      value_template: '{{ as_timestamp(strptime(states("sensor.time_date"), "%H:%M, %Y-%m-%d")) == as_timestamp(strptime(states("sensor.islamic_prayer_time_maghrib"), "%Y-%m-%dT%H:%M:%S")) }}'
    - platform: template
      value_template: '{{ as_timestamp(strptime(states("sensor.time_date"), "%H:%M, %Y-%m-%d")) == as_timestamp(strptime(states("sensor.islamic_prayer_time_isha"), "%Y-%m-%dT%H:%M:%S")) }}'
```

12. Save the file by clicking on the **floppy drive icon** on the top blue bar and then close it by clicking **X** icon.
13. Now on the very left-hand side in the HomeAssistant vertical bar, click on **Configuration** and on right-hand side, click on **server control**. Now under **server management**, click on restart.

> Wait for at least 5 minutes. Close the webpage/home assistant page. Open new browser page, type in your raspberry-Pi IP address along with 8213 as you did previously. HomeAssistant page will appear. Key in your credentials and you will see a dashboard with an **automation** card.

>On this card, you will see **Adhan** and **Fajr Adhan**. Click on **Adhan** and then click on **TRIGGER**. Wait a minute and if you hear adhan from your *google home mini* that's mean everything is successfully setup.

<div align="center">
Hurrah !!! You have made it. Now please remember me in your prayers. جزاک اللہ
</div>
