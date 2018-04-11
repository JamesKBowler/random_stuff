# cheat-sheet

## ESXI
### Disk Cloning
```shell
ls -lsh /vmfs/volumes/
mkdir /vmfs/volumes/destination_datastore/virtual_machine/
vmkfstools -i /vmfs/volumes/src_datastore/vm_name/vm_name.vmdk /vmfs/volumes/dst_datastore/vm_name/vm_name.vmdk -d thin
```

### Raw Device Mapping for local storage
```shell
ls -lsh /vmfs/volumes
vmkfstools -z /vmfs/devices/disks/t10.F405E46494C4540046F455B64787D285941707D203F45765 /vmfs/volumes/Datastore2/localrdm1/localrdm1.vmdk
```

## Ubuntu
```shell
# The primary network interface
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
```
### Setup NFS Share
```shell
# Server
sudo apt install nfs-kernel-server
sudo echo "/path/to/drive     192.168.1.0/24(rw,sync,root_squash,subtree_check)" > /etc/export
sudo service nfs-kernel-server restart
sudo exportfs -ra

# Client
sudo mkdir /mnt/nfs-share
sudo echo "nfs-server-ip:/path/to/drive    /mnt/nfs-share      nfs       rw,soft,intr,noatime,x-gvfs-show" > /etc/fstab
sudo mount -a
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
dd if=kali-linux-light-2016.2-amd64.iso of=/dev/sdb bs=512k
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


