# Mike's Home Assistant Add-ons

[![GitHub Release][releases-shield]][releases]
[![GitHub Actions][actions-shield]][actions]
[![License][license-shield]](LICENSE)

![Project Maintenance][maintenance-shield]
[![GitHub Activity][commits-shield]][commits]

A collection of Home Assistant add-ons maintained by Mike Splain.

### Available Add-ons

This repository contains the following add-ons:

#### Pi-hole

![Latest Version][pihole-version-shield]
![Supports armhf Architecture][armhf-shield]
![Supports aarch64 Architecture][aarch64-shield]
![Supports amd64 Architecture][amd64-shield]

Network-wide ad blocking using Pi-hole and Home Assistant.

### Getting Started

To install these add-ons, you need to add this repository to your Home Assistant:

1. Navigate in your Home Assistant frontend to **Settings** -> **Add-ons** -> **Add-on Store**
2. Click the 3-dots menu at top right -> **Repositories**
3. Add the following URL:

```txt
https://github.com/mikesplain/homeassistant-addons-pihole
```

4. Click **Add**

The add-ons in this repository will now be available in your Home Assistant add-on store.

### Documentation

- [Pi-hole add-on documentation](pi-hole/README.md)

[aarch64-shield]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[armhf-shield]: https://img.shields.io/badge/armhf-yes-green.svg
[armv7-shield]: https://img.shields.io/badge/armv7-yes-green.svg
[pi-hole]: https://pi-hole.net/
[pihole-version-shield]: https://img.shields.io/badge/dynamic/json?label=Version&query=%24.version&url=https%3A%2F%2Fraw.githubusercontent.com%2Fmikesplain%2Fhomeassistant-addons-pihole%2Fmain%2Fpi-hole%2Fconfig.json
[releases-shield]: https://img.shields.io/github/release/mikesplain/homeassistant-addons-pihole.svg
[releases]: https://github.com/mikesplain/homeassistant-addons-pihole/releases
[actions-shield]: https://github.com/mikesplain/homeassistant-addons-pihole/actions/workflows/builder.yaml/badge.svg
[actions]: https://github.com/mikesplain/homeassistant-addons-pihole/actions/workflows/builder.yaml
[license-shield]: https://img.shields.io/github/license/mikesplain/homeassistant-addons-pihole.svg
[maintenance-shield]: https://img.shields.io/maintenance/yes/2025.svg
[commits-shield]: https://img.shields.io/github/commit-activity/y/mikesplain/homeassistant-addons-pihole.svg
[commits]: https://github.com/mikesplain/homeassistant-addons-pihole/commits/main
