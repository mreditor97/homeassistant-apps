# Home Assistant App: Red Reactor Battery Monitor

## Installation

Follow these steps to get the app installed on your system:

1. Navigate in your Home Assistant front-end to **Settings** -> **Apps**.
2. Find the "Red Reactor Battery Monitor" app and click it.
3. Click on the "INSTALL" button.

## How to use

The app has a couple of things that must be configured to get the app running as desired:

1. Configure the MQTT settings.
2. Update app settings to suit your needs.
3. Start the app.
4. Check the app log output to see the result.
5. If MQTT Auto Discovery is enabled, the Red Reactor should appear within your Device list

## Configuration

App configuration:

```yaml
mqtt:
  broker: core-mosquitto # IP Address of your MQTT Broker (optional)
  port: 1883 # Port of your MQTT Broker (optional)
  user: redreactor # Username for your MQTT Broker (optional)
  password: redreactor # Password for your MQTT Broker (optional)
  client_id: redreactor-pi # MQTT client ID, must be unique on the broker (optional)
  tls:
    enabled: false # Encrypt the MQTT connection
    ca_certs: /ssl/ca.crt # CA certificate, leave out to use the system ones (optional)
    certfile: /ssl/client.crt # Client certificate login only (optional)
    keyfile: /ssl/client.key # Client certificate login only (optional)
    insecure: false # Skip the hostname check, for testing only (optional)
hostname:
  name: redreactor-pi # Hostname (used as a MQTT topic level, so no /, + or #)
  pretty: Raspberry Pi # Pretty Display Name
homeassistant:
  discovery: true # Create the Red Reactor device in Home Assistant (optional)
  discovery_interval: 120 # Home Assistant Configuration discovery push interval (seconds, 1 to 86400, 0 for only on connect)
  expire_after: 120 # Home Assistant MQTT Data expiry time (seconds, 0 turns it off, see below)
  status_topic: homeassistant/status # Home Assistant birth message topic, resends data when it restarts (optional)
  status_online: online # Home Assistant birth message payload (optional)
ina:
  address: 64 # I2C address of the Red Reactor, 64 is 0x40, from 3 to 119 (optional)
  monitor_interval: 5 # Seconds between battery readings, 1 to 60 (optional)
  adc_samples: 1 # Samples the INA219 averages for each reading, 1 to 128 (optional)
battery:
  internal_resistance: 35 # Battery internal resistance in mΩ, 0 turns it off (optional)
  average_readings: 6 # Readings averaged for the battery level, 1 to 100, 1 turns it off (optional)
  shutdown_readings: 3 # Low readings in a row before the low battery shutdown, 1 to 20 (optional)
  report_level_change: 5 # Level change in points reported straight away, 0 turns it off (optional)
  curve: [] # Resting voltage to battery level points, empty uses a straight line
system: {} # Shutdown and restart commands, left out to use the Supervisor API (optional)
logging:
  console: INFO
  file: WARNING
```

If you use the Mosquitto broker app, leave `broker`, `user` and `password` empty: the app picks up the broker address and credentials from the Supervisor. Anything you set yourself is used instead.

If the broker can't be reached, the app keeps retrying rather than stopping.

`expire_after` is raised to at least twice the report interval plus 30 seconds, so a report interval set longer from the device page doesn't leave the sensors unavailable between reports. `0` turns expiry off.

`adc_samples` is `1`, `2`, `4`, `8`, `16`, `32`, `64` or `128`. More samples give steadier readings at no CPU cost; 128 takes about 68 ms per reading.

Certificates for TLS go in the Home Assistant `/ssl` folder, which the app can read. Use port `8883` (or whatever your broker uses for TLS) with `tls.enabled: true`.

### Battery level

By default the battery level is a straight line from the battery voltage minimum (0%) to the maximum (100%). A Li-ion battery's voltage doesn't fall evenly as it discharges, and it sags under load, so the `battery` options improve it:

- `internal_resistance` adds back the voltage sag under load (current × resistance) before working out the level. The default of 35 mΩ is a datasheet figure; your measured value is usually higher.
- `average_readings` smooths out brief current spikes by averaging the last few readings.
- `curve` maps the battery's resting voltage to its level. Give at least 2 points with rising voltages and levels that don't fall. The level is interpolated between them, and when a curve is set the battery voltage minimum and maximum no longer affect the level.

For example, for a Samsung INR18650-35E:

```yaml
battery:
  internal_resistance: 35 # mΩ, your measured value
  average_readings: 6
  curve:
    - voltage: 2.80
      level: 0
    - voltage: 3.08
      level: 5
    - voltage: 3.25
      level: 10
    - voltage: 3.39
      level: 15
    - voltage: 3.44
      level: 20
    - voltage: 3.55
      level: 30
    - voltage: 3.63
      level: 40
    - voltage: 3.71
      level: 50
    - voltage: 3.80
      level: 60
    - voltage: 3.89
      level: 70
    - voltage: 3.99
      level: 80
    - voltage: 4.06
      level: 90
    - voltage: 4.08
      level: 95
    - voltage: 4.16
      level: 100
```

This curve was read off the 0.2 A discharge trace in the [lygte-info.dk review of the Samsung INR18650-35E](https://lygte-info.dk/review/batteries2012/Samsung%20INR18650-35E%203500mAh%20%28Pink%29%20UK.html), with the level as the share of the 3.40 Ah the cell gave down to 2.8 V. It was read off a chart, so the voltages are approximate (about ±0.01 V), and your own cells will differ a little with age and temperature. The curve isn't used unless you set it. The [Red Reactor README](https://github.com/mreditor97/redreactor#battery-level) explains how to measure the internal resistance and your own battery's curve.

Other configuration options are available from within the Home Assistant Device configuration page:

- **Battery shutdown level**: when the battery drops to this percentage while running on battery, the Red Reactor safely shuts down Home Assistant. If Home Assistant starts up below this level while the battery is charging, it stays on.
- **Report interval** (default 60 seconds), and the battery voltage minimum and maximum used to work out the battery level.

The monitor interval, readings averaged and shutdown readings are the `ina.monitor_interval`, `battery.average_readings` and `battery.shutdown_readings` app options. The shutdown starts after `battery.shutdown_readings` readings in a row at or below the shutdown level (default 3).

The device also shows the power drawn from the battery, and whether it is charging. When the Red Reactor can't be read 3 times in a row, the battery readings show as unknown until it can be read again.

The battery level is also reported straight away when it moves by `battery.report_level_change` percentage points (default 5), and when charging starts or stops.

Only the first I2C bus (`/dev/i2c-1`) is passed to the app, which is the one the Red Reactor uses on a Raspberry Pi.

## OS Specific I2C configuration

_Depending on the operating system you are using, the configuration may differ as below._

### Home Assistant OS

When using the Home Assistant OS, you must enable I2C. This can be enabled by following the steps from within this [Home Assistant guide](https://www.home-assistant.io/common-tasks/os/#enable-i2c).

_The default configuration for this OS is for UART to be disabled - for the Red Reactor to power your Raspberry Pi, it must be enabled. This is due to the Red Reactor requiring UART assertion. It can be found at the same time as configuring the I2C settings, within the `hassos-boot` partition in `config.txt`. Enable it by adding `enable_uart=1` to the `config.txt`_

### Ubuntu

When using a standard operating system, I2C usually comes enabled. Meaning there is no additional configuration to get the Red Reactor working. If it is not enabled it can be done by ensuring the parameter `dtparam=i2c_arm=on` is contained within `/boot/config.txt` file.
