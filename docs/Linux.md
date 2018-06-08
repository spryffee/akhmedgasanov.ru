 
## Debian 9 (stretch)

sudo dd if=~/Загрузки/debian-9.3.0-amd64-netinst.iso of=/dev/sdb

### Based configuration

You can use [Debian Sources List Generator](https://debgen.simplylinux.ch). Modify the data in the /etc/apt/sources.list file by new data.

`apt update && apt upgrade`

`apt install sudo net-tools`

`adduser [username] sudo` or `visudo` + `username   ALL=(ALL)   ALL`

`apt search [package_name]` - search the needed packages

`dpkg-reconfigure tzdata` - change the timezone

`nano /etc/profile` - set command alias for some commands which are often used

    # add to the end
    alias ll='ls $LS_OPTIONS -l'
    alias l='ls $LS_OPTIONS -lA'
    alias rm='rm -i'
    alias cp='cp -i'
    alias mv='mv -i'

`source /etc/profile`

`ssh-keygen -t ecdsa` - key generation

`ssh -L PORT:localhost:PORT HOSTNAME` - ssh tunnel


`sudo sed -i "/#.*/d" /etc/keystone/keystone.conf` удаляет все строки с решеткой

`sudo sed -i "/^$/d" /etc/keystone/keystone.conf` удаляет пустые строки


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


`route add -net 10.10.0.0/24 dev mgmt1`

`route del -net 10.10.0.0/24 dev mgmt1`

### Troubleshooting

If you get GnuPG errors like:

>GPG error: [..] Release: The following signatures couldn’t be verified because the public key is not available: NO_PUBKEY [..]

`apt update 2>&1 | grep -o '[0-9A-Z]{16}$' | xargs` - try auto update

`egrep -v '^#|^$' /etc/apt/sources.list{,.d/*.list}` - try to find bad repo and do rm manually

`gpg --keyserver subkeys.pgp.net --recv KEY` 
`gpg --export --armor KEY | sudo apt-key add` - or add the key manually

`/etc/apt/trusted.gpg` - or just removing

`fuser -vki /var/lib/dpkg/lock` - to fix error with /var/lib/apt/lists/lock - open 

