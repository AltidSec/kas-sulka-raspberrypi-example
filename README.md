# Sulka Raspberry Pi Reference

This project contains the Raspberry Pi 4 Sulka reference project. The project ports the hardened Sulka distro, kernel and BSP to an actual hardware. The project supports only the default Sulka init manager, systemd.

This integration is achieved with the `kas-sulka-raspberrypi.yml` configuration file that is added alongside the contents of [kas Sulka repository](https://codeberg.org/AltidSec/kas-sulka/). This configuration file adds the [meta-raspberrypi](https://git.yoctoproject.org/meta-raspberrypi) layer as the BSP layer and [meta-sulka-raspberrypi](https://codeberg.org/AltidSec/meta-sulka-raspberrypi) as the Sulka integration layer. In addition, the configuration sets some `local.conf` variables for Raspberry Pi, and sets the target machine as `raspberrypi4-64`.

To build Sulka for the the Raspberry Pi, you build the Sulka almost the same way as usual, just add the `kas-sulka-raspberrypi.yml` to the list of configurations. The following command can be used to build the Raspberry Pi 4 image:

```
kas build kas-sulka.yml:kas-sulka-raspberrypi.yml
```

You can use this repository as an example of how to take Sulka into use in your own projects. In summary, you'll need to do the following things:

1. Fork the [kas Sulka repository](https://codeberg.org/AltidSec/kas-sulka/).
1. Add your own configuration file that sets the machine & target image, configures Sulka, and adds the required meta-layers.
1. If required, create an integration meta-layer that overrides incompatible metadata in Sulka layers.
1. Merge changes from the upstream kas Sulka time to time to stay updated.
