# cheat-sheet

## ESXI
### Disk Cloning
```shell
ls -lsh /vmfs/volumes/
mkdir /vmfs/volumes/destination_datastore/virtual_machine/
vmkfstools -i /vmfs/volumes/src_datastore/vm_name/vm_name.vmdk /vmfs/volumes/dst_datastore/vm_name/vm_name.vmdk -d thin
```

### Raw Device Mapping for local storage


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
sudo echo "/path/to/drive     192.168.1.0/24(rw,sync,root_squash,subtree_check)"
sudo service nfs-kernel-server restart
sudo exportfs -ra

# Client
sudo mkdir /mnt/nfs-share
sudo nano /etc/fstab
nfs-server-ip:/path/to/drive    /mnt/nfs-share      nfs       rw,soft,intr,noatime,x-gvfs-show
sudo mount -a
```

### Check if package installed
```shell
dpkg -l
```

