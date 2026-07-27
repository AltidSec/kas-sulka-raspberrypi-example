# Sulka Raspberry Pi Reference

This project ports Sulka to real hardware: the Raspberry Pi 4, 64-bit. It doubles as the worked example to follow when taking Sulka into your own project.

Sulka is a Yocto Linux distribution that focuses on security hardening.
It ships hardened defaults across the kernel, the bootloader and the userspace, and expects the integrator to consciously relax hardening where their product requires it, rather than the other way round.

Only the default Sulka init manager, `systemd`, is supported in this Raspberry Pi examplpe.

## How This Project Is Put Together

The repository is a fork of [kas Sulka](https://codeberg.org/AltidSec/kas-sulka/), so it carries the whole upstream build configuration and adds the Raspberry Pi integration on top of it.

The main addition is `kas-sulka-raspberrypi.yml`, which:

- sets the target machine to `raspberrypi4-64`,
- adds [meta-raspberrypi](https://github.com/agherzan/meta-raspberrypi) as the BSP layer,
- adds [meta-sulka-raspberrypi](https://codeberg.org/AltidSec/meta-sulka-raspberrypi) as the Sulka integration layer,
- and sets the `local.conf` variables the board needs, including enabling the UART and selecting U-Boot as the bootloader so that the Sulka bootloader hardening applies.

`meta-sulka-raspberrypi` is the integration layer that reconciles Sulka with the board. It applies the Sulka kernel hardening to the `linux-raspberrypi` kernel recipe alongside a board-specific kernel configuration, and adds the U-Boot configuration the Pi requires. Bootloader and kernel metadata are board specific, so this is where most of the porting effort lands.

## Building

The build works the same way as a normal Sulka build, with the Raspberry Pi configuration appended to the list:

```
kas build kas-sulka.yml:kas-sulka-raspberrypi.yml
```

Before the first build you still need to complete the Sulka setup steps, namely setting a password for the service user and generating the module signing keys. Without them the build fails, or produces an image you cannot log in to. See the [quick start guide](https://altidsec.com/sulka/documentation/quick-start.html) for the full procedure.

Graphics are disabled by default and the UART is enabled, so plan on a serial console for access if you do not enable graphics.

## Optional Configuration Fragments

This project adds two fragments of its own to `extra_fragments/`:

| Fragment | Effect |
|---|---|
| `fw-update-rugix.yml` | Adds `meta-rugix` and builds the A/B firmware update image for the Pi. See the [firmware update documentation](https://altidsec.com/sulka/documentation/firmware-update.html). |
| `kas-layers-raspberrypi-development.yml` | Follows the `wrynose-next` development branch of `meta-sulka-raspberrypi` instead of the pinned release tag. The counterpart to the upstream `kas-layers-development.yml`, which does the same for the core Sulka layers, so use both to develop against the whole stack. |

The rest of `extra_fragments/` comes from upstream kas Sulka and is documented in [its README](https://codeberg.org/AltidSec/kas-sulka). Fragments combine, so a development image with firmware update support is:

```
kas build kas-sulka.yml:kas-sulka-raspberrypi.yml:extra_fragments/development.yml:extra_fragments/fw-update-rugix.yml
```

## Using Sulka in Your Own Project

This repository is meant to be read as a template. To take Sulka into your own project, you will need to do the following:

1. Fork the [kas Sulka repository](https://codeberg.org/AltidSec/kas-sulka/).
1. Add your own configuration file that sets the machine and target image, configures Sulka, and adds the required meta-layers. `kas-sulka-raspberrypi.yml` is the example to copy from.
1. If required, create an integration meta-layer that overrides incompatible metadata in the Sulka layers, the way `meta-sulka-raspberrypi` does for the Pi.
1. Merge changes from the upstream kas Sulka from time to time to stay updated.

## Documentation

To get started with Sulka, read [the quick start guide](https://altidsec.com/sulka/documentation/quick-start.html).
More information can be found in [the user guide](https://altidsec.com/sulka/documentation/user-guide.html) and, for the Rugix A/B update example in this repository, the [firmware update guide](https://altidsec.com/sulka/documentation/firmware-update.html).

If the website is unavailable, the same content can be read from [the documentation repository](https://codeberg.org/AltidSec/sulka-docs/src/branch/main/source).

## Contributing

Send pull requests, patches, comments or questions to the AltidSec repositories in Codeberg, and feel free to open issues to start discussions. Use `*-next` branches as pull request targets.

Maintainer:
Esa Jääskelä <esa.jaaskela@suomi24.fi>

## License

The configuration in this repository is licensed under the MIT license. See [COPYING.MIT](COPYING.MIT) for the full text.
The meta-layers and the upstream components fetched during a build carry their own licenses.
