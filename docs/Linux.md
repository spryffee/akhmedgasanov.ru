 
## Debian 9 (stretch)

_dd_  

    sudo dd if=~/Загрузки/debian-9.3.0-amd64-netinst.iso of=/dev/sdb
    dd if=/dev/zero of=ubuntufull.disk bs=1024k count=0 seek=10240

_sed_

`sudo sed -i "/#.*/d" /etc/keystone/keystone.conf` удаляет все строки с решеткой

`sudo sed -i "/^$/d" /etc/keystone/keystone.conf` удаляет пустые строки


### Based configuration

You can use [Debian Sources List Generator](https://debgen.simplylinux.ch). Modify the data in the /etc/apt/sources.list file by new data.

    apt update && apt upgrade
    apt install sudo net-tools
    adduser [username] sudo or visudo + `username   ALL=(ALL)   ALL
    apt search [package_name]
    dpkg-reconfigure tzdata

Set up command alias for some commands which are often used

_nano /etc/profile_

    # add to the end
    alias ll='ls $LS_OPTIONS -l'
    alias l='ls $LS_OPTIONS -lA'
    alias rm='rm -i'
    alias cp='cp -i'
    alias mv='mv -i'
and apply by command _source /etc/profile_


### Networking

    auto virbr0
    iface virbr0 inet static
            address 10.10.0.1/24
            bridge_ports eno1

`apt install firmware-misc-nonfree` - wi-fi driver

    auto wlo1
    iface wlo1 inet dhcp
    wpa-essid [ssid]
    wpa-psk [passwd]

Routes

    route add -net 10.10.0.0/24 dev mgmt1
    route del -net 10.10.0.0/24 dev mgmt1

### Troubleshooting

If you get GnuPG errors like:

>GPG error: [..] Release: The following signatures couldn’t be verified because the public key is not available: NO_PUBKEY [..]

`apt update 2>&1 | grep -o '[0-9A-Z]{16}$' | xargs` - try auto update

`egrep -v '^#|^$' /etc/apt/sources.list{,.d/*.list}` - try to find bad repo and do rm manually

`gpg --keyserver subkeys.pgp.net --recv KEY` 
`gpg --export --armor KEY | sudo apt-key add` - or add the key manually

`/etc/apt/trusted.gpg` - or just removing

`fuser -vki /var/lib/dpkg/lock` - to fix error with /var/lib/apt/lists/lock - open 

