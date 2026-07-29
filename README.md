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

## Celsius And Fractional Temperature Changes

This fork adds the thermostat behavior needed for SmartRent installations
configured in Celsius:

- Temperatures are represented as Celsius in climate and sensor entities.
- The target-temperature step is 0.5 degrees.
- The supported thermostat range is 15 to 30 degrees.
- Heat mode writes the heating setpoint and cool mode writes the cooling
  setpoint.
- Auto mode supports separate fractional low and high targets.
- Fractional temperatures survive initial state loading, commands, and
  websocket updates.

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
5. Open **SmartRent Celsius** and select **Download**.
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

## Verifying Fractional Temperatures

After installation, repeat the checks that exercise each data path:

- Set cooling to 24.5 and confirm Home Assistant, SmartRent, and the physical
  thermostat remain at 24.5 after a websocket update.
- Set heating to a half-degree value such as 21.5.
- In auto mode, set fractional `target_temp_low` and `target_temp_high` values.
- Change a setpoint on the physical thermostat and confirm Home Assistant keeps
  the fractional value.
- Restart Home Assistant with a half-degree target active and confirm the value
  survives initial state loading.

The climate entity should report Celsius and `target_temp_step: 0.5`.

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
