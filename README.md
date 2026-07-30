# SmartRent Home Assistant Component

[![GitHub Release][releases-shield]][releases]
[![HACS Shield][hacs-shield]](https://github.com/hacs/integration)
[![GitHub][license-shield]](LICENSE.md)
[![Code style: black][black-shield]](https://github.com/psf/black)
[![Downloads][downloads-shield]][releases]

[![Open this repository in HACS][hacs-repository-badge]][hacs-repository-link]

> [!WARNING]
> The upstream maintainer moved out of their SmartRent apartment and no longer
> has access to SmartRent hardware. They continue to accept contributed bug-fix
> pull requests.

This is a basic Home Assistant component supporting SmartRent locks,
thermostats, leak sensors, motion sensors, and light switches. It is based on
the original
[`homeassistant-smartrent`](https://github.com/ZacheryThomas/homeassistant-smartrent)
component and uses the
[`smartrent-py`](https://github.com/ZacheryThomas/smartrent-py) library.

Feel free to star this repository to get notified about the latest features.

![example screenshot](dashboard_screenshot.png)

## Fahrenheit Thermostat Setup

Configure both the physical thermostat and Home Assistant to use
**Fahrenheit**. SmartRent's API uses the thermostat's numeric scale, and its
maintenance automation can treat a normal Celsius value such as `24.5` as an
abnormally low Fahrenheit reading. That can create repeated "Abnormal
temperature sensed by Thermostat" service requests.

Set Home Assistant's temperature unit to Fahrenheit under **Settings > System
> General**. The same unit is then used end to end:

- SmartRent values are displayed unchanged in Fahrenheit.
- Fahrenheit commands from Home Assistant are sent unchanged to SmartRent.
- The target-temperature step is 1 degree Fahrenheit.
- The supported thermostat range is 60 to 90 degrees Fahrenheit.
- Heat mode writes the heating setpoint and cool mode writes the cooling
  setpoint.
- Auto mode supports separate low and high targets.

The fractional-value fix lives in the matching
[`Abas-Tim/smartrent-py`](https://github.com/Abas-Tim/smartrent-py) fork. This
integration installs that version directly from its `manifest.json`. No Home
Assistant monkey patch, File Editor change, or manual Python package edit is
required.

## Installation

You can install this integration as an HACS custom component or manually.

### Installing With HACS

[![Open this repository in HACS][hacs-repository-badge]][hacs-repository-link]

To add the repository manually:

1. Back up Home Assistant.
2. Go to HACS, open the menu, and select **Custom repositories**.
3. Enter `https://github.com/Abas-Tim/homeassistant-smartrent`.
4. Select **Integration** as the category and click **Add**.
5. Open **SmartRent** and select **Download**.
6. Restart Home Assistant.
7. Go to **Settings > Devices & services > Add integration**, search for
   **SmartRent**, and enter your SmartRent credentials.

Only this repository needs to be added to HACS. Home Assistant installs the
patched `smartrent-py` dependency automatically during startup.

### Installing Manually

#### Moving The Custom Component To The Correct Directory

```text
# How your HA config directory should look

config
|-- ...
|-- configuration.yaml
|-- secrets.yaml
`-- custom_components
    `-- smartrent
        |-- climate.py
        |-- lock.py
        |-- manifest.json
        `-- ...
```

Move all content in `custom_components/smartrent` to the same location in Home
Assistant. If a `custom_components` directory does not already exist in your
Home Assistant instance, create one. You can learn more in Home Assistant's
[integration file structure documentation](https://developers.home-assistant.io/docs/creating_integration_file_structure#where-home-assistant-looks-for-integrations).

After the files are in place, restart Home Assistant so the component can load.

#### Starting The Integration

Go to **Settings > Devices & services > Add integration**, search for
**SmartRent**, and enter your email and password in the dialog.

## Migrating From The Upstream HACS Integration

1. Create a Home Assistant backup.
2. Remove the original SmartRent repository from HACS, but do not delete the
   SmartRent config entry under **Devices & services**.
3. Add and download this fork using the HACS instructions above.
4. Restart Home Assistant.
5. Confirm that `/config/custom_components/smartrent` contains this fork and
   does not contain the old temporary `thermostat_float_patch.py` file.

## Verifying Thermostat Temperatures

After installation, repeat the checks that exercise each data path:

- Confirm the physical thermostat is set to Fahrenheit before restarting Home
  Assistant.
- Confirm Home Assistant is set to Fahrenheit under **Settings > System >
  General**.
- Confirm a physical reading near 77 degrees appears as 77 degrees Fahrenheit
  in Home Assistant.
- Set cooling to 76 degrees Fahrenheit in Home Assistant and confirm SmartRent
  and the physical thermostat receive 76 degrees Fahrenheit.
- Set heating to a whole-degree Fahrenheit value.
- In auto mode, set whole-degree `target_temp_low` and `target_temp_high`
  values.
- Change a setpoint on the physical thermostat and confirm Home Assistant shows
  the same Fahrenheit value.
- Restart Home Assistant and confirm the values survive initial state loading.

The climate entity should report `temperature_unit: °F` and
`target_temp_step: 1`.

## Credits

The original integration and library were created by
[Zachery Thomas](https://github.com/ZacheryThomas):

- [`ZacheryThomas/homeassistant-smartrent`](https://github.com/ZacheryThomas/homeassistant-smartrent)
- [`ZacheryThomas/smartrent-py`](https://github.com/ZacheryThomas/smartrent-py)

This downstream fork is maintained by
[Abas-Tim](https://github.com/Abas-Tim). The original authorship, MIT copyright,
Git history, project information, screenshot, and acknowledgements are
retained.

[license-shield]: https://img.shields.io/github/license/Abas-Tim/homeassistant-smartrent.svg?style=for-the-badge
[hacs-shield]: https://img.shields.io/badge/HACS-Custom-orange.svg?style=for-the-badge
[black-shield]: https://img.shields.io/badge/code%20style-black-000000.svg?style=for-the-badge
[releases-shield]: https://img.shields.io/github/release/Abas-Tim/homeassistant-smartrent.svg?style=for-the-badge
[releases]: https://github.com/Abas-Tim/homeassistant-smartrent/releases
[downloads-shield]: https://img.shields.io/github/downloads/Abas-Tim/homeassistant-smartrent/total?color=green&style=for-the-badge
[hacs-repository-badge]: https://my.home-assistant.io/badges/hacs_repository.svg
[hacs-repository-link]: https://my.home-assistant.io/redirect/hacs_repository/?owner=Abas-Tim&repository=homeassistant-smartrent&category=integration
