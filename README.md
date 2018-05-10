# cheat-sheet

### Convert VDI to VMDK
```shell
VBoxManage list hdds
VBoxManage clonehd f83fa853-eded-4e67-9927-05fb72544c3d win764.vmdk --format vmdk
```

## ESXI
### Disk Cloning
```shell
ls -lsh /vmfs/volumes/
mkdir /vmfs/volumes/destination_datastore/virtual_machine/
vmkfstools -i /vmfs/volumes/src_datastore/vm_name/vm_name.vmdk /vmfs/volumes/dst_datastore/vm_name/vm_name.vmdk -d thin
```

## VMDK Disk Merge
```shell
vmware-vdiskmanager.exe -r "C:\Users\root\SOURCE\main_disk_in_vmx_file.vmdk" -t 0 "C:\Users\root\DEST\new_merged_disk.vmdk"
```

### Raw Device Mapping for local storage
```shell
ls -lsh /vmfs/volumes
vmkfstools -z /vmfs/devices/disks/t10.F405E46494C4540046F455B64787D285941707D203F45765 /vmfs/volumes/Datastore2/localrdm1/localrdm1.vmdk
```

### Inactive NFS store removal 5.5 >
```shell
esxcli storage nfs list
esxcli storage nfs remove -v nfs_share_name
```


### Copy/Paste from host and between VMs
1. Power off the virtual machine.
2. Click Edit Settings on VM
3. Navigate to Options > Advanced > General and click Configuration Parameters.
4. Click Add Row.  

    Type these values in the Name and Value columns:
    
    | Name                              | Value |
    | --------------------------------- |:-----:|
    | isolation.tools.copy.disable      | FALSE |
    | isolation.tools.paste.disable     | FALSE |



## Ubuntu
```shell
$ nano /etc/network/interfaces

# Wired
auto eth0
iface eth0 inet static
    address 10.0.0.41
    netmask 255.255.255.0
    network 10.0.0.0
    broadcast 10.0.0.255
    gateway 10.0.0.1
    dns-nameservers 10.0.0.1 8.8.8.8
    dns-domain acme.com
    dns-search acme.com

# Wireless
$ wpa_passphrase myssid wpa-password
$ nano /etc/network/interfaces
auto wlan0
iface wlan0 inet dhcp
     wpa-ssid myssid
     wpa-psk {whatever the psk hash was}

$ ifup wlan0


# Manage
$ nmcli dev status
$ ifconfig eth0 down
$ ifconfig eth0 up
```
### Setup NFS Share
```shell
# Server
sudo apt install nfs-kernel-server
sudo echo "/path/to/drive     192.168.1.0/24(rw,sync,root_squash,subtree_check)" >> /etc/export
sudo service nfs-kernel-server restart
sudo exportfs -ra

# If using another drive that needs mounting on reboot

blkid  # to find the UUID

sudo echo 'UUID="2c5dffeb-816a-432b-9df4-5eb0f39b1781"  /mnt/store       ext4    defaults        0       2' >> /etc/fstab

# Client
sudo mkdir /mnt/nfs-share
sudo echo "nfs-server-ip:/path/to/drive    /mnt/nfs-share      nfs       rw,soft,intr,noatime,x-gvfs-show" >> /etc/fstab
sudo mount -a
```
### SMB
```shell
//192.168.6.200/ubuntubox /home/nonroot/ubuntubox cifs guest,uid=1000,iocharset=utf8 0 0
```

### Check if package installed
```shell
dpkg -l
```

### Kali Setup
```shell
# Check CPU bits
grep -qP '^flagss*:.*blmb' /proc/cpuinfo && echo 64-bit || echo 32-bit
# Confirm md5 hash
sha256sum kali-linux-2016.2-amd64.iso

# Make bootable USB on Linux
sudo fdisk -l
dd if=kali-linux-light-2016.2-amd64.iso of=/dev/sdb bs=512k status=progress
# Windows
https://sourceforge.net/projects/win32diskimager/
# Persistance
# Create and format an additional partition on the USB drive
end=7gb
read start _ < <(du -bcm kali-linux-2016.2-amd64.iso | tail -1); echo $start
parted /dev/sdb mkpart primary $start $end
# Encrypt
cryptsetup --verbose --verify-passphrase luksFormat /dev/sdb3
cryptsetup luksOpen /dev/sdb3 my_usb
# create an ext3 file system in the partition and label it “persistence”
mkfs.ext3 -L persistence /dev/mapper/my_usb
e2label /dev/mapper/my_usb persistence
# Mount point
mkdir -p /mnt/my_usb
mount /dev/mapper/my_usb /mnt/my_usb
echo "/ union" > /mnt/my_usb/persistence.conf
umount /dev/mapper/my_usb
# Close the encrypted channel to our persistence partition.
cryptsetup luksClose /dev/mapper/my_usb
```
### Change the hostname
```shell
nano /etc/hostname
nano /etc/hosts
```
### Add none root User
```shell
# Add the user with $HOME directory
useradd -m nonroot
# Add password
passwd nonroot
# Give sudo priv
usermod -a -G sudo nonroot
# Change users shell to bash
chsh -s /bin/bash nonroot
```



### Kali Linux Repositories
https://docs.kali.org/general-use/kali-linux-sources-list-repositories

### Access CLI if GUI is broken
```shell
CTRL+ALT+F1 through CTRL+ALT+F6
```
### General Linux Structure
```
/bin/: basic programs

/boot/: Kali Linux kernel and other files required for its early boot process

/dev/: device files

/etc/: configuration files

/home/: user’s personal files

/lib/: basic libraries

/media/*: mount points for removable devices (CD-ROM, USB keys, and so on)

/mnt/: temporary mount point

/opt/: extra applications provided by third parties

/root/: administrator’s (root’s) personal files

/run/: volatile runtime data that does not persist across reboots (not yet included in the FHS)

/sbin/: system programs

/srv/: data used by servers hosted on this system

/tmp/: temporary files (this directory is often emptied at boot)

/usr/: applications (this directory is further subdivided into bin, sbin, lib according to the same logic as in the root directory) Furthermore, /usr/share/ contains architecture-independent data. The /usr/local/directory is meant to be used by the administrator for installing applications manually without overwriting files handled by the packaging system (dpkg).

/var/: variable data handled by daemons. This includes log files, queues, spools, and caches.

/proc/ and /sys/ are specific to the Linux kernel (and not part of the FHS). They are used by the kernel for exporting data to user space.
```

# System Priv
``` shell
setuid 

chown <user:group file>

chgrp <group> <file>  # alters the owner group

chmod <rights> <file>  # changes the permissions for the file

umask
```

### System Monitoring
```shell
# CPU & Memory
stress # Fake CPU issues
vmstat
free -m
top
htop
# Disk IO Usage
uptime
iotop
iostat
sar
# Network
netstat -ie or ifconfig
netstat -s
netstat -tuna
nload
iftop
speedtest script (python)

# System Information (kernel)
uname -a
/boot
/lib/modules

who
id

# Active modules
lsmod

UUID="2c5dffeb-816a-432b-9df4-5eb0f39b1781"  /mnt/store       ext4    defaults        0       2

journalctl -u ssh.service
```
### Finding stuff
```shell
find / -name <filename>
locate <filename>  # Much faster!

dmesg | grep CPU0:  # CPU
lspci | grep Ethernet ~ Network
lspci -v -s `lspci | grep VGA | cut -f1 -d\ ` # Graphics
uname -r # System
free # Memory
df # Disk
```

### Centos
```shell
sudo yum -y install bash-completion
```


### appImage
```shell
chmod a+x example.AppImage
./example.AppImage
```
