# openwrt网络测速

## iperf测速

```
opkg update
opkg install iperf3
```

#### 服务器端(192.168.0.107)

```
root@ess:~# iperf3 -s
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------

```

#### 客户端(192.168.0.105)

启动客户端测试 （10个客户端并发测试10秒）

```
root@ess:~# iperf3 -c 192.168.0.107 -P 10 -i 1 -p 5201 -f k -t 10
Connecting to host 192.168.0.107, port 5201
[  4] local 192.168.0.105 port 36622 connected to 192.168.0.107 port 5201
[  6] local 192.168.0.105 port 36623 connected to 192.168.0.107 port 5201
[  8] local 192.168.0.105 port 36624 connected to 192.168.0.107 port 5201
[ 10] local 192.168.0.105 port 36625 connected to 192.168.0.107 port 5201
[ 12] local 192.168.0.105 port 36626 connected to 192.168.0.107 port 5201
[ 14] local 192.168.0.105 port 36627 connected to 192.168.0.107 port 5201
[ 16] local 192.168.0.105 port 36628 connected to 192.168.0.107 port 5201
[ 18] local 192.168.0.105 port 36629 connected to 192.168.0.107 port 5201
[ 20] local 192.168.0.105 port 36630 connected to 192.168.0.107 port 5201
[ 22] local 192.168.0.105 port 36631 connected to 192.168.0.107 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec   262 KBytes  2143 Kbits/sec    0   50.9 KBytes
[  6]   0.00-1.00   sec  65.0 KBytes   533 Kbits/sec    5   12.7 KBytes
[  8]   0.00-1.00   sec  65.0 KBytes   533 Kbits/sec    6   12.7 KBytes
[ 10]   0.00-1.00   sec  73.5 KBytes   602 Kbits/sec    3   14.1 KBytes
[ 12]   0.00-1.00   sec  72.1 KBytes   591 Kbits/sec    4   12.7 KBytes
[ 14]   0.00-1.00   sec  79.2 KBytes   648 Kbits/sec    4   15.6 KBytes
[ 16]   0.00-1.00   sec  77.8 KBytes   637 Kbits/sec    4   15.6 KBytes
[ 18]   0.00-1.00   sec   206 KBytes  1691 Kbits/sec    0   48.1 KBytes
[ 20]   0.00-1.00   sec   219 KBytes  1795 Kbits/sec    0   48.1 KBytes
[ 22]   0.00-1.00   sec   184 KBytes  1505 Kbits/sec    7   33.9 KBytes
[SUM]   0.00-1.00   sec  1.27 MBytes  10679 Kbits/sec   33
- - - - - - - - - - - - - - - - - - - - - - - - -
[  4]   1.00-2.00   sec   577 KBytes  4727 Kbits/sec    0   67.9 KBytes
[  6]   1.00-2.00   sec   139 KBytes  1135 Kbits/sec    0   15.6 KBytes
[  8]   1.00-2.00   sec   123 KBytes  1008 Kbits/sec    0   14.1 KBytes
[ 10]   1.00-2.00   sec   124 KBytes  1019 Kbits/sec    0   14.1 KBytes
[ 12]   1.00-2.00   sec   123 KBytes  1008 Kbits/sec    0   14.1 KBytes
[ 14]   1.00-2.00   sec   113 KBytes   927 Kbits/sec    2   12.7 KBytes
[ 16]   1.00-2.00   sec   126 KBytes  1031 Kbits/sec    2   14.1 KBytes
[ 18]   1.00-2.00   sec   543 KBytes  4448 Kbits/sec    0   63.6 KBytes
[ 20]   1.00-2.00   sec   523 KBytes  4286 Kbits/sec    0   72.1 KBytes
[ 22]   1.00-2.00   sec   346 KBytes  2838 Kbits/sec    0   43.8 KBytes
[SUM]   1.00-2.00   sec  2.67 MBytes  22428 Kbits/sec    4
- - - - - - - - - - - - - - - - - - - - - - - - -
[  4]   2.00-3.01   sec   768 KBytes  6249 Kbits/sec   23   42.4 KBytes
[  6]   2.00-3.01   sec   221 KBytes  1795 Kbits/sec    4   15.6 KBytes
[  8]   2.00-3.01   sec   185 KBytes  1508 Kbits/sec    7   14.1 KBytes
[ 10]   2.00-3.01   sec   209 KBytes  1703 Kbits/sec    0   14.1 KBytes
[ 12]   2.00-3.01   sec   232 KBytes  1888 Kbits/sec    0   15.6 KBytes
[ 14]   2.00-3.01   sec   173 KBytes  1404 Kbits/sec    4   14.1 KBytes
[ 16]   2.00-3.01   sec   184 KBytes  1496 Kbits/sec    4   12.7 KBytes
[ 18]   2.00-3.01   sec   792 KBytes  6445 Kbits/sec   13   42.4 KBytes
[ 20]   2.00-3.01   sec   871 KBytes  7090 Kbits/sec   18   43.8 KBytes
[ 22]   2.00-3.01   sec   597 KBytes  4857 Kbits/sec    7   39.6 KBytes
[SUM]   2.00-3.01   sec  4.13 MBytes  34434 Kbits/sec   80
- - - - - - - - - - - - - - - - - - - - - - - - -
[  4]   3.01-4.00   sec   792 KBytes  6530 Kbits/sec    0   48.1 KBytes
[  6]   3.01-4.00   sec   284 KBytes  2344 Kbits/sec    0   17.0 KBytes
[  8]   3.01-4.00   sec   255 KBytes  2099 Kbits/sec    0   15.6 KBytes
[ 10]   3.01-4.00   sec   233 KBytes  1924 Kbits/sec    0   15.6 KBytes
[ 12]   3.01-4.00   sec   247 KBytes  2040 Kbits/sec    0   15.6 KBytes
[ 14]   3.01-4.00   sec   236 KBytes  1947 Kbits/sec    0   14.1 KBytes
[ 16]   3.01-4.00   sec   219 KBytes  1807 Kbits/sec    0   14.1 KBytes
[ 18]   3.01-4.00   sec   817 KBytes  6739 Kbits/sec    0   46.7 KBytes
[ 20]   3.01-4.00   sec   820 KBytes  6762 Kbits/sec    0   49.5 KBytes
[ 22]   3.01-4.00   sec   744 KBytes  6133 Kbits/sec    0   45.3 KBytes
[SUM]   3.01-4.00   sec  4.54 MBytes  38327 Kbits/sec    0
- - - - - - - - - - - - - - - - - - - - - - - - -
[  4]   4.00-5.01   sec   748 KBytes  6090 Kbits/sec   12   43.8 KBytes
[  6]   4.00-5.01   sec   262 KBytes  2130 Kbits/sec    5   17.0 KBytes
[  8]   4.00-5.01   sec   279 KBytes  2268 Kbits/sec    1   9.90 KBytes
[ 10]   4.00-5.01   sec   297 KBytes  2418 Kbits/sec    0   15.6 KBytes
[ 12]   4.00-5.01   sec   300 KBytes  2441 Kbits/sec    0   17.0 KBytes
[ 14]   4.00-5.01   sec   253 KBytes  2061 Kbits/sec    0   14.1 KBytes
[ 16]   4.00-5.01   sec   272 KBytes  2211 Kbits/sec    0   15.6 KBytes
[ 18]   4.00-5.01   sec   731 KBytes  5952 Kbits/sec    6   43.8 KBytes
[ 20]   4.00-5.01   sec   783 KBytes  6378 Kbits/sec    8   39.6 KBytes
[ 22]   4.00-5.01   sec   663 KBytes  5400 Kbits/sec   20   25.5 KBytes
[SUM]   4.00-5.01   sec  4.48 MBytes  37347 Kbits/sec   52
- - - - - - - - - - - - - - - - - - - - - - - - -
[  4]   5.01-6.00   sec   935 KBytes  7705 Kbits/sec    0   48.1 KBytes
[  6]   5.01-6.00   sec   293 KBytes  2413 Kbits/sec    6   17.0 KBytes
[  8]   5.01-6.00   sec   262 KBytes  2156 Kbits/sec    0   14.1 KBytes
[ 10]   5.01-6.00   sec   304 KBytes  2506 Kbits/sec    0   18.4 KBytes
[ 12]   5.01-6.00   sec   325 KBytes  2681 Kbits/sec    0   18.4 KBytes
[ 14]   5.01-6.00   sec   255 KBytes  2098 Kbits/sec    0   14.1 KBytes
[ 16]   5.01-6.00   sec   242 KBytes  1993 Kbits/sec    6   14.1 KBytes
[ 18]   5.01-6.00   sec   940 KBytes  7751 Kbits/sec    0   48.1 KBytes
[ 20]   5.01-6.00   sec   639 KBytes  5268 Kbits/sec    4   29.7 KBytes
[ 22]   5.01-6.00   sec   554 KBytes  4569 Kbits/sec    4   25.5 KBytes
[SUM]   5.01-6.00   sec  4.64 MBytes  39142 Kbits/sec   20
- - - - - - - - - - - - - - - - - - - - - - - - -
[  4]   6.00-7.00   sec   962 KBytes  7877 Kbits/sec   10   41.0 KBytes
[  6]   6.00-7.00   sec   351 KBytes  2873 Kbits/sec    0   17.0 KBytes
[  8]   6.00-7.00   sec   303 KBytes  2479 Kbits/sec    0   15.6 KBytes
[ 10]   6.00-7.00   sec   448 KBytes  3672 Kbits/sec    0   28.3 KBytes
[ 12]   6.00-7.00   sec   348 KBytes  2850 Kbits/sec    3   18.4 KBytes
[ 14]   6.00-7.00   sec   246 KBytes  2016 Kbits/sec    7   12.7 KBytes
[ 16]   6.00-7.00   sec   252 KBytes  2062 Kbits/sec    3   14.1 KBytes
[ 18]   6.00-7.00   sec  1.03 MBytes  8608 Kbits/sec    0   55.1 KBytes
[ 20]   6.00-7.00   sec   699 KBytes  5723 Kbits/sec    0   36.8 KBytes
[ 22]   6.00-7.00   sec   585 KBytes  4796 Kbits/sec    0   31.1 KBytes
[SUM]   6.00-7.00   sec  5.12 MBytes  42955 Kbits/sec   23
- - - - - - - - - - - - - - - - - - - - - - - - -
[  4]   7.00-8.00   sec   717 KBytes  5861 Kbits/sec    6   35.4 KBytes
[  6]   7.00-8.00   sec   321 KBytes  2618 Kbits/sec    0   17.0 KBytes
[  8]   7.00-8.00   sec   313 KBytes  2548 Kbits/sec    0   17.0 KBytes
[ 10]   7.00-8.00   sec   675 KBytes  5500 Kbits/sec    0   46.7 KBytes
[ 12]   7.00-8.00   sec   392 KBytes  3194 Kbits/sec    0   22.6 KBytes
[ 14]   7.00-8.00   sec   267 KBytes  2179 Kbits/sec    0   15.6 KBytes
[ 16]   7.00-8.00   sec   219 KBytes  1787 Kbits/sec    2   12.7 KBytes
[ 18]   7.00-8.00   sec   936 KBytes  7633 Kbits/sec    3   50.9 KBytes
[ 20]   7.00-8.00   sec   665 KBytes  5419 Kbits/sec    2   33.9 KBytes
[ 22]   7.00-8.00   sec   542 KBytes  4416 Kbits/sec    4   29.7 KBytes
[SUM]   7.00-8.00   sec  4.93 MBytes  41246 Kbits/sec   17
- - - - - - - - - - - - - - - - - - - - - - - - -
[  4]   8.00-9.01   sec   617 KBytes  5029 Kbits/sec    2   33.9 KBytes
[  6]   8.00-9.01   sec   304 KBytes  2486 Kbits/sec    0   17.0 KBytes
[  8]   8.00-9.01   sec   279 KBytes  2278 Kbits/sec    8   15.6 KBytes
[ 10]   8.00-9.01   sec   915 KBytes  7482 Kbits/sec    6   49.5 KBytes
[ 12]   8.00-9.01   sec   277 KBytes  2267 Kbits/sec   16   19.8 KBytes
[ 14]   8.00-9.01   sec   256 KBytes  2093 Kbits/sec    5   14.1 KBytes
[ 16]   8.00-9.01   sec   257 KBytes  2105 Kbits/sec    0   15.6 KBytes
[ 18]   8.00-9.01   sec   909 KBytes  7436 Kbits/sec    6   42.4 KBytes
[ 20]   8.00-9.01   sec   711 KBytes  5817 Kbits/sec    0   39.6 KBytes
[ 22]   8.00-9.01   sec   505 KBytes  4129 Kbits/sec    4   26.9 KBytes
[SUM]   8.00-9.01   sec  4.91 MBytes  41030 Kbits/sec   47
- - - - - - - - - - - - - - - - - - - - - - - - -
[  4]   9.01-10.00  sec   529 KBytes  4359 Kbits/sec    5   29.7 KBytes
[  6]   9.01-10.00  sec   307 KBytes  2529 Kbits/sec    0   19.8 KBytes
[  8]   9.01-10.00  sec   276 KBytes  2273 Kbits/sec    0   17.0 KBytes
[ 10]   9.01-10.00  sec   870 KBytes  7168 Kbits/sec    0   55.1 KBytes
[ 12]   9.01-10.00  sec   358 KBytes  2949 Kbits/sec    0   22.6 KBytes
[ 14]   9.01-10.00  sec   245 KBytes  2016 Kbits/sec    0   15.6 KBytes
[ 16]   9.01-10.00  sec   246 KBytes  2028 Kbits/sec    0   15.6 KBytes
[ 18]   9.01-10.00  sec   707 KBytes  5828 Kbits/sec    4   38.2 KBytes
[ 20]   9.01-10.00  sec   635 KBytes  5233 Kbits/sec   13   35.4 KBytes
[ 22]   9.01-10.00  sec   475 KBytes  3916 Kbits/sec    3   25.5 KBytes
[SUM]   9.01-10.00  sec  4.54 MBytes  38302 Kbits/sec   25
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec  6.74 MBytes  5656 Kbits/sec   58             sender
[  4]   0.00-10.00  sec  6.68 MBytes  5602 Kbits/sec                  receiver
[  6]   0.00-10.00  sec  2.49 MBytes  2085 Kbits/sec   20             sender
[  6]   0.00-10.00  sec  2.47 MBytes  2069 Kbits/sec                  receiver
[  8]   0.00-10.00  sec  2.28 MBytes  1915 Kbits/sec   22             sender
[  8]   0.00-10.00  sec  2.27 MBytes  1901 Kbits/sec                  receiver
[ 10]   0.00-10.00  sec  4.05 MBytes  3399 Kbits/sec    9             sender
[ 10]   0.00-10.00  sec  4.01 MBytes  3366 Kbits/sec                  receiver
[ 12]   0.00-10.00  sec  2.61 MBytes  2190 Kbits/sec   23             sender
[ 12]   0.00-10.00  sec  2.59 MBytes  2172 Kbits/sec                  receiver
[ 14]   0.00-10.00  sec  2.07 MBytes  1739 Kbits/sec   22             sender
[ 14]   0.00-10.00  sec  2.06 MBytes  1726 Kbits/sec                  receiver
[ 16]   0.00-10.00  sec  2.05 MBytes  1716 Kbits/sec   21             sender
[ 16]   0.00-10.00  sec  2.03 MBytes  1703 Kbits/sec                  receiver
[ 18]   0.00-10.00  sec  7.45 MBytes  6253 Kbits/sec   32             sender
[ 18]   0.00-10.00  sec  7.37 MBytes  6185 Kbits/sec                  receiver
[ 20]   0.00-10.00  sec  6.41 MBytes  5378 Kbits/sec   45             sender
[ 20]   0.00-10.00  sec  6.31 MBytes  5289 Kbits/sec                  receiver
[ 22]   0.00-10.00  sec  5.07 MBytes  4256 Kbits/sec   49             sender
[ 22]   0.00-10.00  sec  5.03 MBytes  4219 Kbits/sec                  receiver
[SUM]   0.00-10.00  sec  41.2 MBytes  34587 Kbits/sec  301             sender
[SUM]   0.00-10.00  sec  40.8 MBytes  34231 Kbits/sec                  receiver

iperf Done.

```
