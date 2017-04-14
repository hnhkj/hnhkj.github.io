# tcpdump

## write package to a test.pcap

*save tcp's port 6881 to test.pcap in device eth1*

```
$ tcpdump -w test.pcap -i eth1 tcp port 6881
```

*save udp port 33210 & 33220's packet to test.pcap*

```
$ tcpdump -w test.pcap -i eth1 tcp port 6881 or udp\(32210 or 33220\)
```


