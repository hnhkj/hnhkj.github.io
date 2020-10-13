# openwrt network router


# Setup a wireless router

```
Setup a wireless router

While LinkIt Smart 7688 development board is in AP mode, its Wi-Fi and Ethernet interfaces both act as LAN ports. This enables to connect to the board for system configuration. It's possible to configure the Ethernet interface operating on LAN port to act as a WAN port. Therefore, the LinkIt Smart 7688 development board becomes a wireless router when it's still in AP mode.  
Prerequisites

    Breakout boards with an Ethernet port. For example, Seeed Studio provides Breakout for LinkIt Smart 7688 and Arduino Breakout for LinkIt Smart 7688 Duo.
    An Ethernet cable.
    A router with Internet connection.
    Set the LinkIt Smart 7688 development board to the AP mode.

Step-by-step

    Connect the LinkIt Smart 7688 development board with the breakout board.
    Connect the router with Internet connection to the breakout board through an Ethernet cable.
    Power on the board.
    Sign-in to the Web UI.
    Go to OpenWrt's Web UI through the link below the Sign out.
    Sign in the OpenWrt's Web UI with the root password. 

    Select the  Network  tab.

    Click  Edit  under Actions for the  WAN  network on the  Interfaces page. 

    In the Common Configuration page of the WAN interface, select Physical Settings.
    Check the  Ethernet Switch: "eth0"  option.
    Click Save & Apply to start the configuration. 

    Once the configuration is complete, it'll return to the Interfaces page.
    Click Edit under Actions for the button in the LAN network. 
    In the  Common Configuration  page of the LAN interface, select the  Physical Settings .
    Uncheck the creates a bridge over specified interface(s) option.
    Click Save & Apply to start the configuration. 
    Once the configuration is complete, it'll return to the  Interfaces, as shown below. 

    Reboot the device and wait for the system to boot up.

    You've now successfully configured the board (see Wi-Fi LED states). The LinkIt Smart 7688 development board can now be used as a wireless router to access the Internet.

DNS setup

To customize the DNS settings and the domain search option for the LAN port, you can edit /etc/config/dhcp  by adding two parameters ( domain  and dhcp_option ) as follows. In this example, we assume the DNS is 172.20.70.10 and the search domain is mediatek.inc :

config dnsmasq
    ...
    option domain 'mediatek.inc'
    ...

config dhcp 'lan'
    ...
    list 'dhcp_option' '6,172.20.70.10'
    ...

Note

Number 6 before the DNS ('6,172.20.70.10') means the following value is used for assigning the domain name server. Please refer to OpenWrt's Wiki for detailed configurations.

```

<https://docs.labs.mediatek.com/resource/linkit-smart-7688/en/tutorials/network/setup-a-wireless-router>