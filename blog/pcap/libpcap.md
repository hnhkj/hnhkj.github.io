# libpcap

---
title: "libpcap applciation"
author: "huang kongjun"
date: "2016-10-12"
output: html_document
---

# libpcap application

## libpcap code
<https://github.com/the-tcpdump-group/libpcap.git>

## libpcap api

#### pcap_lookupdev(errbuf)
#### pcap_open_live(device, MAXBYTES2CAPTURE,1,512,errbuf);
#### pcap_loop(descr, -1, processPacket, (u_char *)&count);

## reference

#### libpcap used

<http://blog.csdn.net/htttw/article/details/7521053/>
<www.tcpdump.org/manpages/pcap-filter.7.html>
## install libpcap in ubuntu 12.04

#### remove tcpdump

```
$ sudo apt-get remove tcpdump
```

#### install bision-2.4 m4 flex

```
$ sudo apt-get install bison flex m4
```

#### download libpcap-1.1.1

```
$ wget http://tcpdump.org/release/libpcap-1.1.1.tar.gz
```

#### tar libpcap-1.1.1

```
$ tar xzvf libpcap-1.1.1.tar.gz
```

#### complie and insnstall libpcap-1.1.1

```
$ cd libpcap-1.1.1
$ ./configure --prefix=/usr
$ make
$ sudo make install

#### 

