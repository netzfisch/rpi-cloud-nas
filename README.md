# Cloud NAS for the Raspberry PI

Turn your [Raspberry PI](http://raspberrypi.org) within **15 minutes** into a **Cloud Network-Attached-Storage/-Server** allowing **access from remote** networks as well as serving any **Docker Container**.

Inspired by he [The MagPi Magazine](https://magpi.raspberrypi.org/articles/build-a-raspberry-pi-nas) about **creating a NAS with the new Raspberry Pi 4** I added

* [cloud-init](https://cloud-init.io/) for automatic **provisioning of customized cloud instances**,
* [rpi-dyndns](https://github.com/netzfisch/rpi-dyndns) for **dynamic DNS** resolution and
* [rpi-vpn-server](https://github.com/netzfisch/rpi-vpn-server) to **access the NAS** from a remote network and
* based it on [HypriotOS](https://blog.hypriot.com/about/) for **docker host/server** functioniality.

If you find this useful, **do not forget to star** the repository ;-)

## Requirements

- [Raspberry PI](http://raspberrypi.org)
- other [Hardware](https://magpi.raspberrypi.org/articles/build-a-raspberry-pi-nas)
- Dynamic DNS service provider, e.g. from [Securepoint](https://www.spdns.de/)

### Setup

- **Before flashing** check the [user-data.yaml](https://github.com/netzfisch/rpi-private-nas/blob/master/user-data.yaml) file and
- **customise the configuration** to your needs manually, eg. change usernames, passwords, packages, network interface, hard disks, etc.
- Than **Install HypriotOS** a Raspbian based debian derivate, see [bootstrapping with cloud-init](https://blog.hypriot.com/post/cloud-init-cloud-on-hypriot-x64/) and download the image from [here](http://blog.hypriot.com/downloads/)!

```sh
$ curl -LO https://github.com/hypriot/flash/releases/download/2.3.0/flash
$ chmod +x flash
$ ./flash -u user-data.yaml -d /dev/mmcblk0 -f hypriotos-rpi-v1.11.4.img
```

- Put the SD-Card back into the Raspberry and boot. The NAS will be **automatically provisioned** and set up - **repeatable and reliable** :-)
- For configuration of DynDNS and VPN check the repective documentation,
  especially
  - set the update token for [rpi-dyndns](https://github.com/netzfisch/rpi-dyndns),
  - import/generate the secrets for [rpi-vpn-server](https://github.com/netzfisch/rpi-vpn-server), and
  - enable port forwarding at your firewall for the UDP ports 500 and 4500.
- **Done!**

### Debugging

Log into the instance, check the logs at `/var/log/cloud-init-output.log`, and run single commands to verify:

    $ sudo cloud-init single --name users_groups --frequency always

Edit `/boot/user-data`, see the [documentation](https://cloudinit.readthedocs.io/en/18.3/) for details. **Before reboot,** clean up:

    $ sudo rm -R /etc/mdadm/mdadm.conf /etc/samba/smb.conf /mnt/raid1/*
    $ docker stop ddclient vpnserver && docker rm ddclient vpnserver
    $ sudo cloud-init clean --logs --reboot

## TODO

- [ ] reference and load secrets from local environment file
- [ ] add uninterruptible power supply, e.g. USB powerbank
- [ ] add script to act on "power loss" to shutdown server properly

## License

The MIT License (MIT), see [LICENSE](https://github.com/netzfisch/rpi-private-nas/blob/master/LICENSE) file.
