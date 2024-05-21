# raspi_knxd
Ansible-role to install knxd on a raspberry pi

_2024: Since knxd is in the debian repositories for a while now I'll archive this, __consider this as deprecated__!_

# Who needs this?

If you want to make your Raspberry Pi become a cheap KNX-IP-Gateway and/or want it to act as a powerful KNX-Server for heating-control, Smartphone-access, speech-control and other fancy stuff, then read on.

# What else is necessary?

You need some gateway, I use the homemade [selfbus FT1.2 Adapter](http://selfbus.myxwiki.org/xwiki/bin/view/Ger%C3%A4te/Kommunikation/FT12_Raspi), but almost any other will do as well if you change the `knxd_opts:` line in `raspi_knxd/defaults/main.yml`.

You should have basic knowlege about the raspi, Linux-commands, ssh etc.

A Linux-machine to run Ansible on would be great, although it should be possible to just install it on the raspi as well and run it working on localhost. Just expect errors when Ansible restarts it. (And that wouldn't be the point of using Ansible anyways...)

You *don't* necessarily need to know anything about Ansible if you just follow the instructions below.

# How can I use this?

* Install raspbian on your SD-card. I recommend raspbian-lite.
* Make sure your ssh-daemon is running, i.e. by putting an (empty) file `ssh` in the `Boot`-partition
* When making your settings by executing `sudo raspi-config` go to `Interfacing Options` > `P6 Serial` and
  * select `No` when asked to make a login shell accessible over serial and
  * select `yes` when asked to enable the serial port hardware.
* After the reboot I recommend to set up a ssh-key-login to your raspi.

Your raspi-setup is fine now. To roll out the Ansible-role you will need Ansible on your controlling Linux-Machine:

```
sudo apt-get install ansible
```

will do.

Then create `playbooks/raspi_knx.yml`-file with content like this:

```
---
- hosts: knx_raspis
  roles:
   - raspi_knxd
   - raspi_sd_save
```

(In this case I apply man `raspi_sd_save`-role as well).

Furthermore you will need a `hosts`-file like this:

```
knx_raspis:
  hosts:
    hostname_of_my_knxd_raspi
  vars:
    ansible_user: pi
```

and apply all with this command:

```
ansible-playbook -i hosts playbooks/raspi_knxd.yml
```

You can test if it works by sending a telegram to a group-adress from your raspi:

```
knxtool groupswrite ip:localhost 1/1/100 1
```

This will send `1` (="On") to the adress `1/1/100`.
