# ipx800 component for Home Assistant

This a _custom component_ for [Home Assistant](https://www.home-assistant.io/).
The `ipx800` integration allows you to get information and control the [IPX800 v4 and its extensions](http://gce-electronics.com/).

![README en français](README.fr.md) :fr:

## Installation

Copy `custom_components/ipx800` in `config/custom_components` of your Home Assistant.
Add the `ipx800` entry in your `configuration.yml` (see example below).

The IPX800 must be available during Home Assistant start. If you have an other home automation system which query the IPX800, like Jeedom, disable it during start to ensure it will respond to requests.

## Dependency

[pypix800 python package](https://github.com/Aohzan/pypx800) (installed by Home-Assistant itself, nothing to do here)

## Description

You can control by setting the type of the device:

- `relay` as switch and light or climate (with https://www.gce-electronics.com/fr/nos-produits/314-module-diode-fil-pilote-.html)
- `virtualout` as switch and binarysensor
- `virtualin` as switch
- `digitalin` as binarysensor
- `analogin` in as sensor
- `xdimmer` as light
- `xpwm` as light
- `xpwm_rgb` as light (use 3 xpwm channels)
- `xpwm_rgbw` as light (use 4 xpwm channels)
- `x4vr` as cover
- `xthl` as sensors
- `x4fp` as climate

You can update value of a device by set a Push command in a IPX800 scenario. Usefull to update directly binary_sensor and switch.
In `URL ON` and `URL_OFF` set `/api/ipx800/entity_id/state`:

![PUSH configuration example](ipx800_push_configuration_example.png)

## Example

```yaml
# Example configuration.yaml entry
ipx800:
  - name: IPX00
    host: "192.168.1.240"
    api_key: "apikey"
    username: user
    password: password
    scan_interval: 10
    devices:
      - name: Chaudière
        icon: mdi:water-boiler
        type: "relay"
        component: "switch"
        id: 3
      - name: Lumière Garage
        type: relay
        component: light
        id: 9
      - name: Lumière Salle à Manger
        type: xdimmer
        component: light
        id: 1
      - component: light
        name: Spots Cuisine
        type: xpwm
        id: 1
      - component: light
        name: "Bandeau de LED Salon"
        type: xpwm_rgbw
        ids: [9, 10, 11, 12]
        transition: 1.5
      - component: binary_sensor
        device_class: motion
        name: Présence Cuisine
        type: virtualout
        id: 1
      - component: binary_sensor
        name: Sonnette
        type: digitalin
        icon: mdi:bell-circle-outline
        id: 1
      - component: sensor
        device_class: illuminance
        name: Luminosité Cuisine
        icon: mdi:white-balance-sunny
        type: analogin
        id: 1
        unit_of_measurement: "lx"
      - component: sensor
        name: Capteur Rez-de-Chaussée
        type: xthl
        id: 1
      - component: cover
        name: Volet Salon
        type: x4vr
        ext_id: 1
        id: 1
      - component: climate
        name: Radiateur Salon
        type: x4fp
        ext_id: 1
        id: 1
      - component: climate
        name: Radiateur Salle de Bains
        type: relay
        ids: [7, 8]
```

## List of configuration parameters

```yaml
{% configuration %}
name:
  description: Name of the IPX800.
  required: true
  type: name
host:
  description: Hostname or IP address of the IPX800.
  required: true
  type: host
port:
  description: HTTP port.
  required: false
  default: 80
  type: port
api_key:
  description: API key (need to be activate in Network => API)
  required: true
  type: string
username:
  description: Username (for X-PWM control only)
  required: false
  default: VA
  type: string
password:
  description: User's password (for X-PWM control only)
  required: false
  type: string
devices:
  component:
    description: device type
    required: true
    type: string
    values: "switch", "light", "cover", "sensor" or "binary_sensor"
  name:
    description: friendly name of the device
    required: true
    type: string
  device_class:
    description: custom device_class for binary_sensor and sensor only, see Home Assistant
    required: false
    type: string
  unit_of_measurement:
    description: set a unit of measurement for sensor only
    required: false
    type: string
  transition:
    description: transition time in millisecond, for lights only trough X-Dimmer or X-PWM
    required: false
    default: 500
    type: int
  icon:
    description: custom icon
    required: false
    type: string
  # Type to control/Get value, only one otherwise the device will not be added
  type:
    description: type of input/output on the IPX800 or an extension.
    required: true
    type: string
    values: "relay", "analogin", "digitalin", "virtualin", "virtualout", "xdimmer", "xpwm", "xpwm_rgb", "xpwm_rgbw", "xthl", "x4vr", "x4fp", "relay_fp"
  id:
    description: id of type output, required for all except xpwm_rgb and xpwm_rgbw type
    required: false
    type: int
  ext_id:
    description: id of X-4VR extension, required only for x4vr and x4fp type
    required: false
    type: int
  ids:
    description: ids of channel for xpwm_rgb, xpwm_rgbw type or relay as climate component
    required: false
    type: list of int
{% endconfiguration %}
```
