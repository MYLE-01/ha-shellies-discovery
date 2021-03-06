# Shellies Discovery

[![GitHub Release][releases-shield]][releases]
[![GitHub All Releases][downloads-total-shield]][releases]
[![hacs_badge][hacs-shield]][hacs]
[![Community Forum][forum-shield]][forum]
[![Buy me a coffee][buy-me-a-coffee-shield]][buy-me-a-coffee]
[![PayPal_Me][paypal-me-shield]][paypal-me]

![Screenshot](https://github.com/bieniu/ha-shellies-discovery/blob/master/images/shellies-integration.png?raw=true)

This script adds MQTT discovery support for Shellies in the [Home Assistant](https://home-assistant.io/).

This script use Home Assistant [python_script](https://www.home-assistant.io/components/python_script/) component and you have to add it to your `configuration.yaml` file:

```yaml
python_script:
```

You can install this script via [HACS](https://hacs.xyz/) or just download `shellies_discovery.py` file and save it in `/config/python_scripts` folder.

After installing the script and adding automations, run `Shellies Announce` automation or restart Home Assistant twice.

Go to [HA community](https://community.home-assistant.io/t/shellies-discovery-script/94048) for support and help.

## Supported devices

- Shelly 1 (with external sensors)
- Shelly 1PM (with external sensors)
- Shelly 2 (relays and roller mode)
- Shelly 2.5 (relays and roller mode)
- Shelly 3EM
- Shelly 4Pro
- Shelly Air
- Shelly Bulb
- Shelly Button1
- Shelly Dimmer
- Shelly Dimmer 2
- Shelly Door/Window
- Shelly DUO
- Shelly EM
- Shelly Flood
- Shelly Gas
- Shelly H&T (with or without USB adapter)
- Shelly i3
- Shelly Plug
- Shelly Plug S
- Shelly RGBW2 (color and white mode)
- Shelly Sense
- Shelly Smoke
- Shelly Vintage

## How to debug

To debug the script add this to your `logger` configuration:

```yaml
# configuration.yaml file
logger:
  default: warning
  logs:
    homeassistant.components.python_script: debug
    homeassistant.components.automation: info
```

## Troubleshooting checklist

- correct MQTT configuration in Home Assistant with `discovery` enabled
- same `discovery_prefix` in Home Assistant configuration and in script configuration
- Shellies firmware updated to current version
- Home Assistant updated to current version
- enabled MQTT in Shellies configuration
- default topics configuration in Shellies
- default Shellies IDs

## Minimal configuration

```yaml
# configuration.yaml file
python_script:

# automations.yaml file
- id: shellies_announce
  alias: 'Shellies Announce'
  trigger:
    - platform: homeassistant
      event: start
  action:
    service: mqtt.publish
    data:
      topic: shellies/command
      payload: announce

- id: 'shellies_discovery'
  alias: 'Shellies Discovery'
  trigger:
    - platform: mqtt
      topic: shellies/announce
  action:
    service: python_script.shellies_discovery
    data_template:
      id: '{{ trigger.payload_json.id }}'
      mac: '{{ trigger.payload_json.mac }}'
      fw_ver: '{{ trigger.payload_json.fw_ver }}'
```

## Custom configuration example

```yaml
# configuration.yaml file
python_script:

# automations.yaml file
- id: shellies_announce
  alias: 'Shellies Announce'
  trigger:
    - platform: homeassistant
      event: start
  action:
    service: mqtt.publish
    data:
      topic: shellies/command
      payload: announce

- id: 'shellies_discovery'
  alias: 'Shellies Discovery'
  trigger:
    - platform: mqtt
      topic: shellies/announce
  action:
    service: python_script.shellies_discovery
    data_template:
      id: '{{ trigger.payload_json.id }}'
      mac: '{{ trigger.payload_json.mac }}'
      fw_ver: '{{ trigger.payload_json.fw_ver }}'
      discovery_prefix: 'hass'
      qos: 2
      shelly1-AABB9900:
        relay-0: "light"
        ext-0: "temperature"
        ext-1: "temperature"
        ext-2: "temperature"
        force_update_sensors: true
      shelly1pm-aabb9911:
        ext-0: "temperature"
        push_off_delay: false
        force_update_sensors: true
      shellyswitch-123409FF:
        relay-0: "fan"
        relay-0-name: "Bathroom Fan"
        relay-1: "light"
        relay-1-name: "Livingroom Light"
      shellyswitch-123409cc:
        relay-1: "fan"
      shellydimmer-883409cc:
        light-0-name: "Bedroom Lamp"
      shellyswitch25-334455AA:
        mode: "roller"
      shellyplug-s-CCBBCCAA:
        relay-0: "light"
        force_update_sensors: true
      shellyht-11AA00CCDD:
        force_update_sensors: true
      shellyht-11AA00CCEE:
        powered: "battery"
      shellyht-11AA00CCFF:
        powered: "ac"
      shellyrgbw2-AA123FF32:
        mode: "white"
      shellyrgbw2-AA123FF84:
        mode: "rgbw"
      shellyem-BB23CC45:
        force_update_sensors: true
      ignored_devices:
        - shelly1-DD0011
        - shellyem-EECC22
```

## Script arguments

key | optional | type | default | description
-- | -- | -- | -- | --
`discovery_prefix` | True | string | `homeassistant` | MQTT discovery prefix
`qos` | True | integer | `0` | MQTT QoS, you can use `0`, `1` or `2`
`ignored_devices` | True | list | `None` | list of devices to ignore

## Device arguments

key | optional | type | default | possible values | description
-- | -- | -- | -- | -- | --
`relay-<NUM>` | True | string | `switch` | `switch`, `light`, `fan` | component to use with the relay number `NUM`
`relay-<NUM>-name` | True | string | None | string | friendly name of the relay number `NUM`
`roller-<NUM>-name` | True | string | None | string | friendly name of the roller number `NUM`
`light-<NUM>-name` | True | string | None | string | friendly name of the light number `NUM`
`ext-<NUM>` | True | string | `None` | `temperature` | type of external sensor number `NUM`
`force_update_sensors` | True | boolean | `false` | `true`, `false` | [force update](https://www.home-assistant.io/integrations/sensor.mqtt/#force_update) for sensors
`push_off_delay` | True | boolean | `true` | `true`, `false` | [off delay](https://www.home-assistant.io/integrations/binary_sensor.mqtt/#off_delay) (2 sec) for `longpush`/`shortpush` binary sensors
`mode` | True | string | | `white`, `rgbw`, `relay`, `roller` | `white` or `rgbw` for Shelly RGBW2, `relay` or `roller` for Shelly 2/Shelly 2.5
`powered` | True | string | `battery` | `ac`, `battery` | `ac` or `battery` powered for Shelly H&T


[releases]: https://github.com/bieniu/ha-shellies-discovery/releases
[releases-shield]: https://img.shields.io/github/release/bieniu/ha-shellies-discovery.svg?style=popout
[downloads-total-shield]: https://img.shields.io/github/downloads/bieniu/ha-shellies-discovery/total
[forum]: https://community.home-assistant.io/t/shellies-discovery-script/94048
[forum-shield]: https://img.shields.io/badge/community-forum-brightgreen.svg?style=popout
[buy-me-a-coffee-shield]: https://img.shields.io/static/v1.svg?label=%20&message=Buy%20me%20a%20coffee&color=6f4e37&logo=buy%20me%20a%20coffee&logoColor=white
[buy-me-a-coffee]: https://www.buymeacoffee.com/QnLdxeaqO
[paypal-me-shield]: https://img.shields.io/static/v1.svg?label=%20&message=PayPal.Me&logo=paypal
[paypal-me]: https://www.paypal.me/bieniu79
[hacs-shield]: https://img.shields.io/badge/HACS-Default-orange.svg
[hacs]: https://hacs.xyz/docs/default_repositories
