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

# Matplotlib with LATEX fonts

```
sudo apt-get install texlive-latex-extra texlive-fonts-recommended dvipng cm-super
sudo apt install msttcorefonts -qq
```

on header:

```
plt.rcParams.update({
    "text.usetex": True,
    "font.family": "sans-serif",
    "font.sans-serif": ["Helvetica"]})
# for Palatino and other serif fonts use:
plt.rcParams.update({
    "text.usetex": True,
    "font.family": "serif",
    "font.serif": ["Palatino"],
})
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

# Update to more recent kernel

```
add-apt-repository ppa:cappelikan/ppa -y
apt update
apt install mainline
mainline --list
```

Select the desired kernel version

```
mailnine --install <version>
```

## Other method

add-apt-repository ppa:canonical-kernel-team/proposed -y
apt update

### To install normal kernel
apt install linux-headers-5.15.*-*-generic linux-image-5.15.*-*-generic

### To install low-latency kernel
apt install linux-headers-5.15.*-*-generic* linux-image-5.15.*-*-lowlatency


# Realtime Kernel (Ubuntu 22)

Follow the "Build Environment" instructions
https://wiki.ubuntu.com/Kernel/BuildYourOwnKernel

You might need to uncomment `deb-src` entries in `/etc/apt/sources.list`

```
sed -i '/^# deb-src/s/^# //' /etc/apt/sources.list
```

```
apt install -y libncurses-dev flex bison openssl libssl-dev dkms libelf-dev libudev-dev libpci-dev libiberty-dev autoconf fakeroot

wget https://mirrors.edge.kernel.org/pub/linux/kernel/v6.x/linux-6.6.5.tar.xz && \
wget https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/6.6/patch-6.6.5-rt16.patch.xz
```

Unpack source and apply patch:

```
mkdir kernel && \
mv linux-6.6.5.tar.xz patch-6.6.5-rt16.patch.xz kernel && \
cd kernel && \
tar -xvf linux-6.6.5.tar.xz && \
xz -d patch-6.6.5-rt16.patch.xz

cd linux-6.6.5 && \
patch -p1 < ../patch-6.6.5-rt16.patch
```

Copy old kernel config and adjust configuration:

```
cp /boot/config-$(uname -r) .config

yes '' | make oldconfig && \
make menuconfig
```

```
# Enable CONFIG_PREEMPT_RT
 -> General Setup
  -> Preemption Model (Fully Preemptible Kernel (Real-Time))
   (X) Fully Preemptible Kernel (Real-Time)

# Enable CONFIG_HIGH_RES_TIMERS
 -> General setup
  -> Timers subsystem
   [*] High Resolution Timer Support

# Enable CONFIG_NO_HZ_FULL
 -> General setup
  -> Timers subsystem
   -> Timer tick handling (Full dynticks system (tickless))
    (X) Full dynticks system (tickless)

# Set CONFIG_HZ_1000 (note: this is no longer in the General Setup menu, go back twice)
 -> Processor type and features
  -> Timer frequency (1000 HZ)
   (X) 1000 HZ

# Set CPU_FREQ_DEFAULT_GOV_PERFORMANCE [=y]
 ->  Power management and ACPI options
  -> CPU Frequency scaling
   -> CPU Frequency scaling (CPU_FREQ [=y])
    -> Default CPUFreq governor (<choice> [=y])
     (X) performance

```

Generate certificates (https://superuser.com/questions/1214116/no-openssl-sign-file-signing-key-pem-leads-to-error-while-loading-kernel-modules/1322832#1322832 )

```
echo -e "[ req ] \n\
default_bits = 4096 \n\
distinguished_name = req_distinguished_name \n\
prompt = no \n\
x509_extensions = myexts \n\

[ req_distinguished_name ] \n\
CN = Modules \n\
\n\
[ myexts ] \n\
basicConstraints=critical,CA:FALSE \n\
keyUsage=digitalSignature \n\
subjectKeyIdentifier=hash \n\
authorityKeyIdentifier=keyid" > x509.genkey
```

```
openssl req -new -nodes -utf8 -sha512 -days 36500 -batch -x509 -config x509.genkey -outform DER -out certs/signing_key.x509 -keyout certs/signing_key.pem
```

Edit `.config` and comment:

```
CONFIG_SYSTEM_TRUSTED_KEYRING=y
CONFIG_SYSTEM_TRUSTED_KEYS="debian/canonical-certs.pem"
CONFIG_SYSTEM_REVOCATION_LIST=y
CONFIG_SYSTEM_REVOCATION_KEYS="debian/canonical-revoked-certs.pem"
```

Build and install kernel

```
make -j `nproc` bzImage
make -j `nproc` modules
make INSTALL_MOD_STRIP=1 modules_install
make install
```

If you get an error of no space left on disk, it might be the /boot partition being filled with the initrd. Use the following steps to reduce the size:

```
cd /lib/modules/<new_kernel>
find . -name *.ko -exec strip --strip-unneeded {} +
```

https://www.howtoforge.com/how-to-install-linux-kernel-6-on-ubuntu-22-04/
https://docs.ros.org/en/foxy/Tutorials/Miscellaneous/Building-Realtime-rt_preempt-kernel-for-ROS-2.html


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

# Running processes on specific CPU cores

https://www.xmodulo.com/run-program-process-specific-cpu-cores-linux.html


# Compiling kernel

https://itsubuntu.com/how-to-compile-and-install-kernel-on-ubuntu/

If you find problems during compilation with some ubuntu keys thing...

https://askubuntu.com/questions/1329538/compiling-the-kernel-5-11-11

# Reducing size of initrd

```
SHW@SHW:/tmp# cd /lib/modules/<new_kernel>
SHW@SHW:/tmp# find . -name *.ko -exec strip --strip-unneeded {} +
```

# Changing hostname permanently

```
sudo hostnamectl set-hostname <new_hostname>
```

Edit `/etc/hosts`

```
127.0.1.1    <new_hostname>
```

Reboot

# Setup NAT

```
echo 1 > /proc/sys/net/ipv4/ip_forward

iptables -t nat -A POSTROUTING -o <iface to internet> -j MASQUERADE
iptables -A FORWARD -i <iface to internal> -j ACCEPT
```
https://www.thomaslaurenson.com/blog/2018/07/05/building-a-ubuntu-linux-gateway/

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

# Ubuntu Server with KDE - Network manager not showing wired connections

In `/etc/NetworkManager/NetworkManager.conf`:

Change:
```
[ifupdown]
managed=true
```

Add:
```
[keyfile]
unmanaged-devices=*,except:type:wifi,except:type:wwan,except:type:ethernet
```

Add user to netdev group:

```
sudo usermod -aG netdev username
```

Restart NetworkManager
```
systemctl restart NetworkManager
```

# Create swap space

```
sudo fallocate -l 16G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

# Issues upgrading from 22.10

Run this, then the normal update/upgrade/do-release-upgrade
```
sudo sed -i -r 's/([a-z]{2}.)?archive.ubuntu.com/old-releases.ubuntu.com/g' /etc/apt/sources.list
```
```
sudo sed -i -r 's/security.ubuntu.com/old-releases.ubuntu.com/g' /etc/apt/sources.list
```

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

## Proxmox resizing

In proxmox, if you do not see the free space listed, run a device rescan with:

```
echo 1> /sys/class/block/<device>/device/rescan
```

Once done, you can use `cfdisk` to resize the partition, then use `pvresize /dev/sdX`

https://packetpushers.net/blog/ubuntu-extend-your-default-lvm-space/


# Hostapd and DHCP Server

https://ppn.snovvcrash.rocks/admin/networking/dhcp-hostapd
