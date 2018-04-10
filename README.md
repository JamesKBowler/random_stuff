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

### Check if package installed
```shell
dpkg -l
```

