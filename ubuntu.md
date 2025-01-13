# Graphical boot

Disable: `systemctl set-default multi-user.target`

Enable: `systemctl set-default graphical.target`

To start a Gnome session on a system without a current GUI

```
systemctl start gdm3.service
```

# Set oldstyle interface names

Edit
```
/etc/default/grub
```

Add on `GRUB_CMDLINE_LINUX`
```
net.ifnames=0 biosdevname=0
```

Run
```
update-grub
```

# Network configuration with netplan

## Examples

Edit:
```
vim /etc/netplan/01-netcfg.yaml
```

```
network:
  version: 2
  ethernets:
    enp0s3:
     dhcp4: no
     addresses: [192.168.1.222/24]
     routes: 
       - to: default
         via: 10.10.10.1
     nameservers:
       addresses: [8.8.8.8,8.8.4.4]
```

```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: yes
```

Apply configuration:

```
netplan apply
```

# Sudo without password

As root, edit sudoers: `visudo`

Add to the end of the file, where username is the name of the user...
```
username   ALL=(ALL) NOPASSWD:ALL
```

# Reduce size of reserved space

Sets to 2%
```
tune2fs -m2 /dev/partition
```

# Graphical interface on Ubuntu Server

```
apt install tasksel
```

List what can be installed
```
tasksel --list-tasks
```
```
tasksel install kde-desktop
reboot
systemctl set-default graphical.target
```

# Change default kernel on grub

1. Find GRUB entry on `/boot/grub/grub.cfg`
```
grep submenu /boot/grub/grub.cfg
```

Example output:
```
submenu 'Advanced options for Ubuntu' $menuentry_id_option 'gnulinux-advanced-4591a659-55e2-4bec-8dbe-d98bd9e489cf' {
```

2. Get kernel option:
```
grep gnulinux-<version> /boot/grub/grub.cfg
```

Example output:
```
menuentry 'Ubuntu, with Linux 4.15.0-126-generic' --class ubuntu --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-4.15.0-126-generic-advanced-4591a659-55e2-4bec-8dbe-d98bd9e489cf' {
```

3. Merge parts of the outputs using a `>`

`gnulinux-advanced-4591a659-55e2-4bec-8dbe-d98bd9e489cf>gnulinux-4.15.0-126-generic-advanced-4591a659-55e2-4bec-8dbe-d98bd9e489cf`

4. Edit `/etc/default/grub` and set the above line on `GRUB_DEFAULT`

Example:
```
GRUB_DEFAULT='gnulinux-advanced-4591a659-55e2-4bec-8dbe-d98bd9e489cf>gnulinux-4.15.0-126-generic-advanced-4591a659-55e2-4bec-8dbe-d98bd9e489cf'
```

5. Run `update-grub`

# Changing hostname permanently

```
sudo hostnamectl set-hostname <new_hostname>
```

Edit `/etc/hosts`

```
127.0.1.1    <new_hostname>
```

Reboot

# Disable NetworkManager from running on specific interface

Edit `/etc/NetworkManager/NetworkManager.conf`

```
[keyfile]
unmanaged-devices=interface-name:wlan0
```

Then run
```
systemctl restart NetworkManager
```

# Disable mitigations

Add `mitigations=off` to `GRUB_CMDLINE_LINUX_DEFAULT` on `/etc/default/grub`

Example:
```
GRUB_CMDLINE_LINUX_DEFAULT=”quiet splash mitigations=off“
```

# Disable unattended upgrades

```
dpkg-reconfigure unattended-upgrades
```

OR

Edit `/etc/apt/apt.conf.d/20auto-upgrades`

Set `APT::Periodic::Unattended-Upgrade "0";`

# Create swap space

sudo fallocate -l 16G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Resize LVM

Check current volume group configuration

```
vgdisplay
```

Extend volume group to use the full size

Check LV_PATH with `lvdisplay`

```
lvextend -l +100%FREE <LV_PATH>
```

Extend the partition

```
resize2fs </dev/mapper.../ubuntu--lv>
```

https://packetpushers.net/blog/ubuntu-extend-your-default-lvm-space/
