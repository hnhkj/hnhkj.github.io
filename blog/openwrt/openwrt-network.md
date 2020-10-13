# openwrt network

## LINKIT7688网络配置

LINKIT7688网络默认配置为交换机模式，网口和WIFI都作为LAN口。我们的需要是计划配置成路由器模式。网口作为WAN口，AP作为LAN。这个配置在LINKIT7688官网有详细讲解。我将文章做了保存。[Setup a wireless router.doc]<https://docs/labs/mediatek.com/resource/linkit-smart-7688/en/tutorials/network/setup-a-wireless-router>


#### 配置过程主要分为2步

连接WIFI，进入openwrt的web界面(192.168.100.1)，选择network/interface

* WAN/Physical Settings - 选择`Eth0` ，`Save & Apply`
* LAN/Physical Settings - 取消`creates a bridge over specified interface(s)`，`Save & Apply`，注意，由于应用后7688的网络配置发生了改变，WIFI连接断开，所以网页不能自动刷新

重新启动后路由器模式工作。





## network mode

* Router mode
* Bridge mode


## Router mode

```sh

```

## Bridge


```sh
root@ess:~# cat /etc/config/network 

config interface 'loopback'
	option ifname 'lo'
	option proto 'static'
	option ipaddr '127.0.0.1'
	option netmask '255.0.0.0'

config globals 'globals'
	option ula_prefix 'fd21:494d:2c1c::/48'

config interface 'lan'
	option ifname 'eth0'
	option force_link '1'
	option macaddr '00:0c:43:e1:76:2a'
	option type 'bridge'
	option proto 'static'
	option netmask '255.255.255.0'
	option ip6assign '60'
	option ipaddr '192.168.0.200'

config switch
	option name 'switch0'
	option reset '1'
	option enable_vlan '0'

config interface 'wan'
	option proto 'dhcp'

root@ess:~# cat /etc/config/wireless 

config wifi-device 'radio0'
	option type 'ralink'
	option variant 'mt7628'
	option country 'TW'
	option hwmode '11g'
	option htmode 'HT40'
	option channel 'auto'
	option linkit_mode 'ap'
	option disabled '0'

config wifi-iface 'ap'
	option device 'radio0'
	option mode 'ap'
	option network 'lan'
	option ifname 'ra0'
	option encryption 'none'
	option ssid 'ess_450286'
	option seq '1'

config wifi-iface 'sta'
	option device 'radio0'
	option mode 'sta'
	option network 'wan'
	option ifname 'apcli0'
	option led 'mediatek:orange:wifi'
	option ssid 'UplinkAp'
	option key 'SecretKey'
	option encryption 'psk'

root@ess:~# cat /etc/config/dhcp 

config dnsmasq
	option domainneeded '1'
	option boguspriv '1'
	option filterwin2k '0'
	option localise_queries '1'
	option rebind_protection '1'
	option rebind_localhost '1'
	option local '/lan/'
	option domain 'lan'
	option expandhosts '1'
	option nonegcache '0'
	option authoritative '1'
	option readethers '1'
	option leasefile '/tmp/dhcp.leases'
	option resolvfile '/tmp/resolv.conf.auto'
	option localservice '1'

config dhcp 'lan'
	option interface 'lan'
	option start '100'
	option limit '150'
	option leasetime '12h'
	option dhcpv6 'server'
	option ra 'server'

config dhcp 'wan'
	option interface 'wan'
	option ignore '1'

config odhcpd 'odhcpd'
	option maindhcp '0'
	option leasefile '/tmp/hosts/odhcpd'
	option leasetrigger '/usr/sbin/odhcpd-update'

root@ess:~# cat /etc/config/firewall 
config defaults
	option syn_flood	1
	option input		ACCEPT
	option output		ACCEPT
	option forward		REJECT
# Uncomment this line to disable ipv6 rules
#	option disable_ipv6	1

config zone
	option name		lan
	list   network		'lan'
	option input		ACCEPT
	option output		ACCEPT
	option forward		ACCEPT

config zone
	option name		wan
	list   network		'wan'
	list   network		'wan6'
	option input		REJECT
	option output		ACCEPT
	option forward		REJECT
	option masq		1
	option mtu_fix		1

config forwarding
	option src		lan
	option dest		wan

# We need to accept udp packets on port 68,
# see https://dev.openwrt.org/ticket/4108
config rule
	option name		Allow-DHCP-Renew
	option src		wan
	option proto		udp
	option dest_port	68
	option target		ACCEPT
	option family		ipv4

# Allow IPv4 ping
config rule
	option name		Allow-Ping
	option src		wan
	option proto		icmp
	option icmp_type	echo-request
	option family		ipv4
	option target		ACCEPT

config rule
	option name		Allow-IGMP
	option src		wan
	option proto		igmp
	option family		ipv4
	option target		ACCEPT

# Allow DHCPv6 replies
# see https://dev.openwrt.org/ticket/10381
config rule
	option name		Allow-DHCPv6
	option src		wan
	option proto		udp
	option src_ip		fe80::/10
	option src_port		547
	option dest_ip		fe80::/10
	option dest_port	546
	option family		ipv6
	option target		ACCEPT

config rule
	option name		Allow-MLD
	option src		wan
	option proto		icmp
	option src_ip		fe80::/10
	list icmp_type		'130/0'
	list icmp_type		'131/0'
	list icmp_type		'132/0'
	list icmp_type		'143/0'
	option family		ipv6
	option target		ACCEPT

# Allow essential incoming IPv6 ICMP traffic
config rule
	option name		Allow-ICMPv6-Input
	option src		wan
	option proto	icmp
	list icmp_type		echo-request
	list icmp_type		echo-reply
	list icmp_type		destination-unreachable
	list icmp_type		packet-too-big
	list icmp_type		time-exceeded
	list icmp_type		bad-header
	list icmp_type		unknown-header-type
	list icmp_type		router-solicitation
	list icmp_type		neighbour-solicitation
	list icmp_type		router-advertisement
	list icmp_type		neighbour-advertisement
	option limit		1000/sec
	option family		ipv6
	option target		ACCEPT

# Allow essential forwarded IPv6 ICMP traffic
config rule
	option name		Allow-ICMPv6-Forward
	option src		wan
	option dest		*
	option proto		icmp
	list icmp_type		echo-request
	list icmp_type		echo-reply
	list icmp_type		destination-unreachable
	list icmp_type		packet-too-big
	list icmp_type		time-exceeded
	list icmp_type		bad-header
	list icmp_type		unknown-header-type
	option limit		1000/sec
	option family		ipv6
	option target		ACCEPT

# include a file with users custom iptables rules
config include
	option path /etc/firewall.user


### EXAMPLE CONFIG SECTIONS
# do not allow a specific ip to access wan
#config rule
#	option src		lan
#	option src_ip	192.168.45.2
#	option dest		wan
#	option proto	tcp
#	option target	REJECT

# block a specific mac on wan
#config rule
#	option dest		wan
#	option src_mac	00:11:22:33:44:66
#	option target	REJECT

# block incoming ICMP traffic on a zone
#config rule
#	option src		lan
#	option proto	ICMP
#	option target	DROP

# port redirect port coming in on wan to lan
#config redirect
#	option src			wan
#	option src_dport	80
#	option dest			lan
#	option dest_ip		192.168.16.235
#	option dest_port	80
#	option proto		tcp

# port redirect of remapped ssh port (22001) on wan
#config redirect
#	option src		wan
#	option src_dport	22001
#	option dest		lan
#	option dest_port	22
#	option proto		tcp

# allow IPsec/ESP and ISAKMP passthrough
config rule
	option src		wan
	option dest		lan
	option proto		esp
	option target		ACCEPT

config rule
	option src		wan
	option dest		lan
	option dest_port	500
	option proto		udp
	option target		ACCEPT

### FULL CONFIG SECTIONS
#config rule
#	option src		lan
#	option src_ip	192.168.45.2
#	option src_mac	00:11:22:33:44:55
#	option src_port	80
#	option dest		wan
#	option dest_ip	194.25.2.129
#	option dest_port	120
#	option proto	tcp
#	option target	REJECT

#config redirect
#	option src		lan
#	option src_ip	192.168.45.2
#	option src_mac	00:11:22:33:44:55
#	option src_port		1024
#	option src_dport	80
#	option dest_ip	194.25.2.129
#	option dest_port	120
#	option proto	tcp


```


