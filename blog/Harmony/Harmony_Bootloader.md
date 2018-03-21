# Harmony Bootloader Ӧ��

#### ͨѶЭ��

**�ο�`MPLaB Harmony Help`�е�`Bootloader Communication Protocol (UART, USB HID, and Ethernet)`���֣���ͨѶЭ�������˵����**


```
Command Value in Hexadecimal  Description  
0x01  Read the Bootloader version information.  
0x02  Erase the Flash.  
0x03  Program the Flash.  
0x04  Read the CRC.  
0x05  Jump to the application.  
```



```
[<SOH>��]<SOH>[<0x01>]<CRCL><CRCH><EOT>
```

```
01 10 01 00 00 04
```