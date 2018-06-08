 


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


CVE-2016-0792

CVE-2015-8103





alert http $EXTERNAL_NET any -> $HOME_NET any (msg:"ET WEB_SPECIFIC_APPS Jenkins Script Console Usage (Metasploit Unix Shell)"; flow:to_server,established; content:"POST"; http_method; nocase; content:"/script"; http_uri; nocase; pcre:"/\/script\/?$/Ui"; content:"sun.misc.BASE64Decoder"; nocase; http_client_body; content:".decodeBuffer"; nocase; http_client_body; content:"/bin/sh"; http_client_body; fast_pattern; classtype:attempted-user; sid:2016296; rev:7; metadata:affected_product Any, attack_target Client_and_Server, deployment Perimeter, deployment Internet, deployment Internal, deployment Datacenter, tag Metasploit, signature_severity Critical, created_at 2013_01_24, updated_at 2016_07_01;)

alert http $EXTERNAL_NET any -> $HOME_NET any (msg:"ET WEB_SPECIFIC_APPS Possible Jenkins CLI RCE (CVE-2017-1000353)"; flow:to_server,established; content:"POST"; http_method; nocase; content:"/cli"; http_uri; depth:4; content:"Side|3a 20|upload"; http_header; content:"JENKINS REMOTING CAPACITY]===>rO0ABXNyABpodWRzb24ucmVtb3RpbmcuQ2FwYWJ"; http_client_body; fast_pattern; metadata: former_category WEB_SPECIFIC_APPS; reference:url,blogs.securiteam.com/index.php/archives/3171; reference:cve,2017-1000353; reference:url,research.checkpoint.com/jenkins-miner-one-biggest-mining-operations-ever-discovered/; classtype:attempted-user; sid:2025376; rev:2; metadata:created_at 2018_02_21, updated_at 2018_02_21;)

alert tcp any any -> $HOME_NET any (msg:"ET EXPLOIT Serialized Java Object Calling Common Collection Function"; flow:to_server,established; content:"rO0ABXNyA"; content:"jb21tb25zLmNvbGxlY3Rpb25z"; fast_pattern; distance:0; reference:url,github.com/foxglovesec/JavaUnserializeExploits; classtype:misc-activity; sid:2022114; rev:1; metadata:created_at 2015_11_17, updated_at 2015_11_17;)

alert http any any -> $HOME_NET any (msg:"ET EXPLOIT Apache Struts 2 REST Plugin XStream RCE (ProcessBuilder)"; flow:to_server,established; content:"java.lang.ProcessBuilder"; nocase; http_client_body; fast_pattern; content:"<command"; nocase; distance:0; http_client_body; pcre:"/^[\s>]/RPs"; metadata: former_category EXPLOIT; reference:cve,2017-9805; reference:url,lgtm.com/blog/apache_struts_CVE-2017-9805_announcement; classtype:attempted-user; sid:2024663; rev:1; metadata:affected_product Apache_Struts2, attack_target Web_Server, deployment Perimeter, signature_severity Critical, created_at 2017_09_06, performance_impact Low, updated_at 2017_09_06;)

alert http any any -> $HOME_NET any (msg:"ET EXPLOIT Apache Struts 2 REST Plugin XStream RCE (Runtime.Exec)"; flow:to_server,established; content:"java.lang.Runtime"; nocase; http_client_body; fast_pattern; content:".exec"; distance:0; http_client_body; content:"<command"; nocase; distance:0; http_client_body; pcre:"/^[\s>]/RPs"; metadata: former_category EXPLOIT; reference:cve,2017-9805; reference:url,lgtm.com/blog/apache_struts_CVE-2017-9805_announcement; classtype:attempted-user; sid:2024664; rev:1; metadata:affected_product Apache_Struts2, attack_target Web_Server, deployment Perimeter, signature_severity Critical, created_at 2017_09_06, performance_impact Low, updated_at 2017_09_06;)





cve 2016-1209

alert http $EXTERNAL_NET any -> $HOME_NET any (msg:"ET WEB_SERVER PHP tags in HTTP POST"; flow:established,to_server; content:"POST"; nocase; http_method; content:"<?php"; nocase; http_client_body; fast_pattern:only; reference:url,isc.sans.edu/diary.html?storyid=9478; classtype:web-application-attack; sid:2011768; rev:6; metadata:created_at 2010_09_28, updated_at 2010_09_28;)
2011768 || ET WEB_SERVER PHP tags in HTTP POST || url,isc.sans.edu/diary.html?storyid=9478




alert http $EXTERNAL_NET any -> $HOME_NET any (msg:"ET WEB_SPECIFIC_APPS Jenkins Script Console Usage (Metasploit Unix Shell)"; flow:to_server,established; content:"POST"; http_method; nocase; content:"/script"; http_uri; nocase; pcre:"/\/script\/?$/Ui"; content:"sun.misc.BASE64Decoder"; nocase; http_client_body; content:".decodeBuffer"; nocase; http_client_body; content:"/bin/sh"; http_client_body; fast_pattern; classtype:attempted-user; metadata:created_by_sne_students)

alert http $EXTERNAL_NET any -> $HOME_NET any (msg:"ET WEB_SPECIFIC_APPS Possible Jenkins CLI RCE (CVE-2017-1000353)"; flow:to_server,established; content:"POST"; http_method; nocase; content:"/cli"; http_uri; depth:4; content:"Side|3a 20|upload"; http_header; content:"JENKINS REMOTING CAPACITY]===>rO0ABXNyABpodWRzb24ucmVtb3RpbmcuQ2FwYWJ"; http_client_body; fast_pattern; metadata: former_category WEB_SPECIFIC_APPS; reference:url,blogs.securiteam.com/index.php/archives/3171; reference:cve,2017-1000353; reference:url,research.checkpoint.com/jenkins-miner-one-biggest-mining-operations-ever-discovered/; classtype:attempted-user; sid:2025376; rev:2; metadata:created_at 2018_02_21, updated_at 2018_02_21;)

alert tcp any any -> $HOME_NET any (msg:"ET EXPLOIT Serialized Java Object Calling Common Collection Function"; flow:to_server,established; content:"rO0ABXNyA"; content:"jb21tb25zLmNvbGxlY3Rpb25z"; fast_pattern; distance:0; reference:url,github.com/foxglovesec/JavaUnserializeExploits; classtype:misc-activity; sid:2022114; rev:1; metadata:created_at 2015_11_17, updated_at 2015_11_17;)

alert http any any -> $HOME_NET any (msg:"EXPLOIT DETECTED REST Plugin XStream RCE (ProcessBuilder)"; flow:to_server,established; content:"java.lang.ProcessBuilder"; nocase; http_client_body; fast_pattern; content:"<command"; nocase; distance:0; http_client_body; pcre:"/^[\s>]/RPs"; metadata: former_category EXPLOIT; reference:cve,2017-9805; reference:url,lgtm.com/blog/apache_struts_CVE-2017-9805_announcement; classtype:attempted-user; sid:2024663; rev:1; metadata:created_by_SNE_students)
