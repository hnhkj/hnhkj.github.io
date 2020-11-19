# Asterisk问题解答


## 应用

#### 字符匹配

* X - 匹配任意单数字0-9
* Z - 匹配任意单数字1-9
* N - 匹配任意单数字2-9
* [15-7] - 匹配1,5,6,7
* .(period)
* !(bang)

<https://blog.csdn.net/u013132673/article/details/52571369>

#### 点击ESS的SW2按钮，ESS自动拨打一个电话出去

* 添加按钮处理功能到`/etc/rc.button/BTN_2`

```
> cat /etc/rc.button/BTN_2
#!/bin/sh

[ "${ACTION}" = "released" -o "${ACTION}" = "timeout" ] || exit 21

. /lib/functions.sh

#EXT_HELP="*1009"
EXT_HELP="0100011"
#EXT_HELP="0100000"

asterisk -rx "console dial $EXT_HELP"
echo "CALL $HELP_EXT ..." > /dev/tty1

return 0
```

* 修改`/etc/asterisk/alsa.con`的context。

```
> cat /etc/asterisk/alsa.conf
;
; Default context (is overridden with @context syntax)
;
context=help
;
; Default extension to call
;
```

* 修改`/etc/asterisk/sip.conf`

```
> cat /etc/asterisk/sip.conf
[general]
...
register => 100012:UwPo583nuY@sip.call4help.uk
...
[call4help]
type=peer
host=sip.call4help.uk
username=100012
secret=UwPo583nuY
insecure=invite
dtmfmode = rfc2833
disallow=all
allow=ulaw
allow=alaw
fromuser=100012
nat=force_rport,comedia
canreivite = no
```

* 修改`/etc/asterisk/extensions.conf`

```
> cat /etc/asterisk/extensions.conf
...
[help]
exten => 0100011,1,Dial(SIP/${EXTEN:1}@call4help)
    same => n,Hangup()

exten => 0100000,1,Dial(SIP/${EXTEN:1}@call4help)
    same => n,Hangup()
```


#### Connecting an Asterisk system to SIP provider//连接一个Asterisk系统到SIP提供者

* `修改/etc/asterisk/sip.conf`

```
[general]
...
register => username:password@your.provider.tld
...

[myprovider]

type = peer
host = your.provider.tld
defaultuser = username
secret = password
insecure = invite
dtmfmode = rfc2833
disallow = all
allow = ulaw
```

* 修改`/etc/asterisk/extensions.conf`

```
exten => _1NXXNXXXXXX,1,Dial(SIP/${EXTEN}@myprovider)
```

参考 Page 176 `Asterisk The Definitive Guide 4th Edition`