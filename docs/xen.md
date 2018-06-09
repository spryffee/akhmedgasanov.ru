
###Xen installation

Install xen and set up grub priority. Check the virtualization extensions. 
If not already enabled in BIOS, enable it.

    apt-get install xen-system-amd64 xen-tools      
    mv -i /etc/grub.d/20_linux_xen /etc/grub.d/08_linux_xen
    update-grub
    xl dmesg | grep VMX

We also can prepare lvm partition

    apt-get install lvm2
    vgcreate vg0 /dev/sda7


Configure the mirrors.conf and xen-tools.conf files as needed. 

If OvS is used then _nano xl.conf_

	vif.default.script="vif-openvswitch"
	vif.default.bridge="xenbr0"

Some xen commands:

    xl list
    xl create /etc/xen/<hostname>.cfg
    xl console <hostname>
    xl restart <hostname>
    xen-delete-image VMs_name --lvm=vg01
    xl network-list <hostname>

Create PV DomU using xen-tools:
        
    xen-create-image --hostname=$guest --ip x.x.x.x --netmask x.x.x.x --gateway x.x.x.x --vcpus 2 --dist stretch
    xen-create-image --hostname=$guest --memory=2gb --vcpus=2 --lvm=vg0 --dhcp --pygrub --dist=stretch


Create HVM DomU manually:

`lvcreate -n name -L 20G lvmgroup`

`nano /etc/xen/web.cfg`                                                 

	builder = "hvm"
	name = "web-hvm"
	memory = "2048"
	vcpus = 2
	vif = [ 'script=vif-openvswitch,bridge=xenbr0' ]
	disk = ['phy:/dev/vg0/web,hda,w','file:/srv/share/ubuntu-14.04.1-server-amd64.iso,hdc:cdrom,r']
	#disk = ['phy:/dev/vg0/web,hda,w']
	vnc = 1
	#boot="c"
	boot="dc"



###Cold Migration

`xl save <domain id> <savefile>`

`scp <savefile> <target.domain.tld:/path/>`

`xl restore <savefile>`

###Live Migration

`nano /etc/xen/xend-config.sxp`

	(xend-relocation-server yes)

`/etc/init.d/xend restart`

`xl migrate <domain id> <destination machine>`

###HA Cluster

`xl remus -F -n -d 6 10.10.0.20`