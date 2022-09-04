
![GitHub Logo](/image.jpg)

# Home Assistant Tuya integration with BHT-002 thermostat support

## Overview

Me and many others are frustrated that the BHT-002 thermostat (sometimes called Moes or Beca Energy)
is showing a 5 times too small temperature using the official Tuya integration.

Examples of issues reported in the Home Assistant Core issue tracker are 
[Issue 72703](https://github.com/home-assistant/core/issues/72703) and
[Issue 73612](https://github.com/home-assistant/core/issues/73612).

The reason for the issue with BHT-002 is that the thermostat does not
follow the Tuya Open API specification. The temperature values to/from
the thermostat should be divided by 10 in spec but BHT-002 divides temperature
with 2.

I made a workaround in the Tuya integration for this issue found 
[in the clone here](https://github.com/midstar/homeassistant_core/tree/tuya_BHT-002_thermostat_workaround).
This workaround is only applied to BHT-002 thermostats based on their ID and thus other
thermostats will work as expected.

However, the responsible authors of the Home Assistant Tuya integration refuse to include
my changes since they won't include any device specific parts in their implementation.

Fortunately, the changes are possible to install as a custom component that overrides
the official integration.

## Installation instruction

### Step 1 - Uninstall the Tuya integration

If you have installed the Tuya integration. Delete it in the Home Assistant configuration
user interface.

### Step 2 - Enable SSH

If you are using Home Assistant OS (HASSIO) secure that you have SSH addon up and running.
Follow the instruction [here](https://community.home-assistant.io/t/home-assistant-community-add-on-ssh-web-terminal/33820)

### Step 3 - Download the modified Tuya integration

Clone [the modified files](https://github.com/midstar/homeassistant_core/tree/tuya_BHT-002_thermostat_workaround) and
checkout the tuya_BHT-002_thermostat_workaround branch.

```
joel@penguin:~/temp$ git clone git@github.com:midstar/homeassistant_core.git
Cloning into 'homeassistant_core'...
...
joel@penguin:~/temp$ cd homeassistant_core/
joel@penguin:~/temp/homeassistant_core$ git checkout tuya_BHT-002_thermostat_workaround 
Branch 'tuya_BHT-002_thermostat_workaround' set up to track remote branch 'tuya_BHT-002_thermostat_workaround' from 'origin'.
Switched to a new branch 'tuya_BHT-002_thermostat_workaround'
joel@penguin:~/temp/homeassistant_core$ cd homeassistant/components/tuya/
```

### Step 4 - Modify the manifest

The manifest.json file (in tuya directory) needs to include a version key/value pair.
Add this key/value. It doesn't really matter which version you pick. For example:

```json
{
  "domain": "tuya",
  "name": "Tuya",
  "documentation": "https://www.home-assistant.io/integrations/tuya",
  "requirements": ["tuya-iot-py-sdk==0.6.6"],
  "dependencies": ["ffmpeg"],
  "codeowners": ["@Tuya", "@zlinoliver", "@frenck"],
  "config_flow": true,
  "iot_class": "cloud_push",
  "dhcp": [
    { "macaddress": "105A17*" },
    { "macaddress": "10D561*" },
    { "macaddress": "1869D8*" },
    { "macaddress": "381F8D*" },
    { "macaddress": "508A06*" },
    { "macaddress": "68572D*" },
    { "macaddress": "708976*" },
    { "macaddress": "7CF666*" },
    { "macaddress": "84E342*" },
    { "macaddress": "D4A651*" },
    { "macaddress": "D81F12*" }
  ],
  "loggers": ["tuya_iot"],
  "version": "1.0.0"
}
```

### Step 5 - Upload the custom component

Put the modified tuya directory in the config/custom_components directory. You need to
create a custom_components directory unless you already have it.

```
scp -R tuya/ root@192.168.1.103:/root/config/custom_components/
```

Note that "root/config/custom_componens" is the correct directory for Home Assistant OS.
Other directories apply to other installation types. Also "root" user might be different 
in your system. Of course you need to modify the IP address to your Home Assistant IP.

### Step 6 - Configure the Tuya custom component

Restart Home Assistant. Then add the integration under configuration in the Home Assistant
user interface, i.e. you add it in the same way as you add the official integration. 
Since you have tuya in custom_components it will override the official integration.
Follow the official [Tuya configuration guide](https://www.home-assistant.io/integrations/tuya/).

## Issues

I will try to keep the custom integration up to date with the official Tuya integration.

Please let me know if you find any issues, would like to add more device specific parts
into the code or if there are fixes in the official Tuya integration that should be
merged.
