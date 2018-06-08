 


### Basic configuration

Физический интерфейс, включаемый в ovs-sw0, не должен иметь IP-адреса или любых других настроек.

Один коммутатор не должен иметь больше одного интерфейса по причине использования OvS свичем STP протокола.

Использовать несколько физических интерфейсов можно только если они будут агрегированы.

`ovs-vsctl add-br ovs-sw0`

`ovs-vsctl add-port ovs-sw0 eth0`

`ovs-vsctl add-port ovs-sw0 mgmt0`

`ovs-vsctl set interface mgmt0 type=internal`

`ovs-vsctl show`

nano /etc/network/interfaces

	auto lo
	iface lo inet loopback

	auto enp1s0
	iface enp1s0 inet manual
    
	allow-hotplug mgmt0
	iface mgmt0 inet static
	address pub_ip/27
	gateway pub_gw
	dns-nameservers 77.88.8.8

	allow-hotplug mgmt1
	iface mgmt1 inet static
	address 10.10.0.1/24


`ovs-vsctl list-br`

`ovs-vsctl list-ports ovs-sw0`

`ovs-vsctl del-port ovs-sw0 mgmt0`

`ovs-vsctl del-br ovs-sw0`

### VLAN

`ovs-vsctl set port <имя_порта> tag=10`

`ovs-vsctl add-br sw0-vlan10 ovs-sw0 10`

Разрешим порту eth0 пропускать во внешний мир трафик из определенных VLAN-ов:

ovs-vsctl set port eth0 trunks=100,200,300

Вместо команды set, могут также использоваться:

	list <таблица> <запись>
	find <таблица> <условие>
	get <таблица> <запись> <ключ=значение>
	add <таблица> <запись> <ключ=значение>
	remove <таблица> <запись> <ключ=значение>
	clear <таблица> <запись> <ключ
	create <таблица> <запись> <ключ=значение>
	destroy <таблица> <запись>
	wait-until <таблица> <запись> <ключ=значение>

смотрим конфигурацию для конкретно интерфейса:

`ovs-vsctl list port eth0`

Поиск по параметрам, например по vlan:

`ovs-vsctl find port tag=100`

### Агрегируем линки (LACP)

`ovs-vsctl add-bond <имя_коммутатора> <имя_нового_bonda> <список_физ.интерфейсов>`

`ovs-vsctl add-bond ovs-sw0 bond0 eth0 eth1`

`ovs-vsctl set port bond0 lacp=active`

`ovs-appctl lacp/show <bond-интерфейс>`

###Mirroring

`ovs-vsctl -- set Bridge xenbr1 mirrors=@m \
-- --id=@enp1s0 get Port enp1s0 \
-- --id=@mirror1 get Port mirror1 \
-- --id=@m create Mirror name=enp1s0-mirror select-dst-port=@enp1s0 \
select-src-port=@enp1s0 output-port=@mirror1`

